# 🧠 TransformRepoWithSpecs.DesignSpec.md

## 📘 Transform Repo With Specs Design Specification

---

## 📚 Purpose

This pipeline automates the generation or modification of source code in a GitHub repository using a combination of design specifications and a user-provided natural language prompt. The AI model synthesizes the current state of the system (from specs and code) and produces code outputs that are either new files or in-place modifications. The modified source is committed and pushed to a new feature branch derived from the development branch.

---

## 📥 Inputs

```yaml
- name: SpecDirectory
  type: string
  required: true
  description: Path to a folder containing all relevant design specifications (e.g., *.DesignSpec.md)

- name: GitRemoteUrl
  type: string
  required: true
  description: HTTPS or SSH URL of the GitHub repository to clone and transform

- name: Prompt
  type: string
  required: true
  description: Natural language instruction directing the AI to generate or modify code using the provided specs and repository context
```

---

## 📤 Outputs

- Updated or newly generated source code files written to the feature branch of the cloned repository
- `.zip` archive of all changed or generated files saved to `./output/TransformRepoResult.zip`
- AI prompt audit trail saved to `./output/last-used-prompt.txt`
- Snapshot copy of design specs used, saved to `./output/WorkingSpecs/`
- List of files shown to the AI saved to `./output/ShownToAi.log`

---

## 📊 ContextHints (Optional DesignSpec Section)

Used to guide the pipeline in selecting relevant files from the repo. When present, this section informs the `AnalyzeRepoAsync` logic.

```yaml
object-type: CommandService
primary-files:
  - {CommandName}Command.cs
  - {CommandName}CommandHandler.cs
include-related-folder:
  - Services/{CommandName}Service/
```

---

## ♻️ Processing Flow

1. Begin pipeline execution with `ActionLog.Global.BeginStep("Transforming repository with AI")`
2. Clone repo from `GitRemoteUrl` into a temporary working directory
3. Checkout `development` branch and create `feature/ai-transform-{timestamp}` (via `clone-repo`)  
   🔧 Not implemented yet — handled inside `CloneRepoCommand`
4. Validate inputs
5. Copy specs from document store to `WorkingSpecs/` folder inside the working directory (delegates to future `CopyFromStoreCommand`)  
   🔧 Not implemented yet — will abstract spec staging from pipeline logic
6. Log available specs and target repo contents
7. Parse existing files in the cloned repo and narrow scope using `ContextHints` if available  
   🔧 Placeholder: eventually may delegate to `InferContextFromFileCommand`
8. Construct a detailed AI prompt using:
   - User instruction
   - Parsed spec list from `WorkingSpecs/`
   - Repo file previews
9. Save the full prompt to `last-used-prompt.txt` and the file list to `ShownToAi.log`
10. Log the prompt preview and submit to `IAiCompletionService`
11. Parse AI response into `{ file path, content | delete }` results
12. For each file result:
    - Log `Step("Added: {path}")` if new
    - Log `Step("Updated: {path}")` if modified
13. Write modified and added files to disk
14. Track deletions in `TransformDeletedFiles.log` and apply deletions separately  
    🧩 Split into `ApplyDeletionsAsync` for clarity and governance
15. Commit changes and push to remote `feature/ai-transform-{timestamp}`  
    🔧 Not implemented yet — will delegate to `PushToRepoCommand` in SoftwareFactory
16. Zip the result to `./output/TransformRepoResult.zip`, including `WorkingSpecs/`, deleted log, prompt, and `ShownToAi.log`
17. Log `Success("Repo transformation complete")`

---

## 🧩 Decomposed Design

- Input validation → `ValidateContextAsync`
- CloneRepo → `CloneAndPrepareBranchAsync`  
  🔧 Not implemented yet — will delegate to `CloneRepoCommand` in SoftwareFactory
- CopySpecsToWorkingDir → `CopyFromStoreAsync`  
  🔧 Not implemented yet — will delegate to `CopyFromStoreCommand` in SoftwareFactory
- LogContext → `LogWorkingContextAsync`
- LoadSpecs → `LoadSpecsAsync`
- ParseRepo → `AnalyzeRepoAsync`  
  🔧 May delegate to `InferContextFromFileCommand` for advanced context resolution
- PromptBuilder → `BuildTransformPromptAsync`
- ExecuteAI → `RunAiTransformationAsync`
- WriteOutput → `ApplyTransformResultsAsync`
- ApplyDeletions → `ApplyDeletionsAsync`
- CommitAndPush → `PushFeatureBranchAsync`  
  🔧 Not implemented yet — will delegate to `PushToRepoCommand` in SoftwareFactory
- ZipResults → `ZipOutputAsync`

---

## 🧠 Logging & Telemetry

- All orchestration and SRP methods begin with `ActionLog.Global.BeginStep(...)`
- Each file operation (add, update, delete) is logged with `Step(...)`
- Prompt and file list used for generation are saved and zipped for review
- Final success message is emitted with `ActionLog.Global.Success(...)`
