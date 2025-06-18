# 🧠 TransformRepoWithSpecs.CommandService.Contract.md

## 📘 Purpose

Defines the contract used when executing the `TransformRepoWithSpecs` pipeline with `TransformationType = create-command-service`. It outlines the AI-facing inputs, the expected file generation output, and repository file cleanup behavior.

---

## 📥 Inputs to AI

The following inputs are provided to the AI in a structured format:

```yaml
InputsToAi:
  SolutionName: The name of the solution (e.g., Sandlot.AiPipeline)
  ProjectName: The name of the project within the solution that will receive the transformation
  TransformationType: The type of transformation being performed (e.g., create-command-service)
  ServiceName: The PascalCase name of the service being created or modified
  DesignSpecPath: Relative path to the business logic design specification within the filestore (e.g., specs/LogHelloWorldService.DesignSpec.md) The PascalCase name of the service being created or modified
```

The PascalCase name of the service being created or modified

---

## 📐 Design Pattern Specification

This transformation is governed by a set of canonical design pattern documents that define the expected structure and behavior of all generated components. These documents must be used as both input to the AI and guidance for the human developer interpreting and reviewing the results.

The following four documents define the command-service pattern for this contract:

#### ✅ DesignPatternReferences (YAML Format)

```yaml
Standards:
  - Standards\CommandService.DesignPattern.Standard.v2.md
  - Standards\CommandService.DesignPattern.DecisionMatrix.v2.md
  - Standards\CommandService.AiPipeline.DesignSpec.v2.md
  - Standards\DesignSpec.Factory.Standard.v2.md
```

These documents:

- Define folder and file structure (Standard)
- Provide construction defaults for orchestrators and SRPs (DecisionMatrix)
- Define expected input/output and AI behavior for command-service pipelines (AiPipeline)
- Define the grammar and structure for all `.DesignSpec.md` inputs (DesignSpec.Factory)

These are required for both AI prompt context and human QA.

---

## 🗑️ Files to Remove from Repository

This section identifies tokenized path patterns that will be deleted from the repository before applying the transformation.

All paths are relative to the root of the repository.`

Token definitions:

- `{ProjectName}`: Target C# project name
- `{ServiceName}`: PascalCase name used for both command and service

#### ✅ FilesToDelete (YAML Format)

```yaml
FilesToDelete:
  - src/{ProjectName}/Commands/{ServiceName}Command.cs
  - src/{ProjectName}/CommandHandlers/{ServiceName}CommandHandler.cs
  - src/{ProjectName}/Services/{ServiceName}
```

---
