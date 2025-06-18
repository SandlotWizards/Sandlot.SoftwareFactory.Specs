# 🧱 CommandService Design Pattern Standard

## ⚠ Canonical Enforcement Notice
This document defines a **canonical design pattern standard** for all Command-Service units within the Sandlot Software Factory.  
All implementations using this pattern shall conform to the structural, behavioral, and organizational principles described herein.  
This pattern is the foundational building block for AI pipelines, product factories, and orchestrated service logic.

---

## 📘 Purpose
To standardize the architecture of command-driven service units by defining clear boundaries between command responsibilities, handler orchestration, and core business logic.

---

## 🗂 Scope
This pattern applies to all command-service components whether built manually, via AI pipeline, or through CLI tooling.  
It governs their structure, folder layout, naming conventions, and execution responsibilities.

---

## 🧱 Output File and Folder Structure

Each Command-Service unit shall consist of the following files:

```
/Commands/
  {ServiceName}Service_Command.cs

/CommandHandlers/
  {ServiceName}Service_CommandHandler.cs

/Services/{ServiceName}Service/
  {ServiceName}Service.cs                  <-- Orchestrator
  {MethodName}.cs                          <-- SRP methods (one per file)
```

---

## 📄 Orchestrator Definition (`{ServiceName}Service.cs`)
- Declares the `partial class` for the service
- Contains the constructor with all dependency injection
- Contains a single method named `OrchestrateAsync` or `Orchestrate` (sync/async)
- Method **visibility** should be `internal` unless a specific scenario requires otherwise
- The orchestrator **must call `ValidateContextAsync` first** if implemented
- **The orchestrator must begin with a scoped `ActionLog.Global.BeginStep(...)` statement.**

---

## 🧩 SRP Partial Method Rules
- Each method must be in its own file named `{MethodName}.cs`
- Each method implements a single use case or logical responsibility
- Each method must begin with `ActionLog.Global.BeginStep(...)`
- Do not prefix log step titles with manual numbering (e.g., "1.1")
- Optional nested steps may use `ActionLog.Global.Step(...)` when semantically meaningful
- Private helper methods are allowed within each file but must remain minimal
- Method files must not declare shared mutable properties or expose global state

---

## 🔐 State & Property Guidelines
- Shared state across partials is strongly discouraged
- Only `readonly` DI fields declared in the orchestrator are allowed
- Any high-level mutable property must be explicitly justified and documented due to test coupling risk

---

## ⚠ Context Validation (`ValidateContextAsync`)
- Optional but strongly recommended
- Validates required preconditions for execution (e.g., file existence, user auth, external service availability)
- Must be the **first method** invoked by `Orchestrate*`
- If omitted, the design must explicitly address the risks of partial execution

---

## 🚫 Partial Execution Risk
If required preconditions are not enforced in `ValidateContextAsync` and instead fail during mid-execution, the service risks entering a **partial execution state**. This may:
- Cause data or process inconsistency
- Require compensation logic
- Be considered an architectural failure unless documented and mitigated

---

## 🔍 Structured Logging Requirements
- All services must follow the `ActionLogger.Factory.Usage.Standard.md`
- Logging must use `ActionLog.Global.BeginStep(...)` in orchestrators and all partial methods
- `ActionLog.Global.Step(...)` is optional but encouraged when clarity benefits
- Manual numbering in step titles is prohibited
- All code assumes `ActionLog.Global` has been initialized by the host

---

## 🧠 Continuous Improvement
This design pattern may evolve to support additional concepts such as structured validators, response builders, or domain-level conventions. All changes must preserve orchestration clarity and service isolation.
