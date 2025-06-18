# 🧠 CommandService Decision Matrix (beta1)

## ⚠ Canonical Enforcement Notice
This document defines the **permissible decisions and default behaviors** for AI agents (e.g., Woodson) when constructing Command-Service units—specifically for generating the `{ServiceName}Service_Command.cs` and `{ServiceName}CommandHandler.cs` files.  
Each decision point defines allowable options and the expected default behavior. This matrix supports clarity, consistency, and predictable output in AI-assisted software generation.

---

## 📘 Purpose
To establish explicit and delegated rules for how each part of a Command-Service unit should be constructed, including syntax-level decisions like visibility, inheritance, code structure, and behavior.

---

## 🗂 Scope
This decision matrix applies exclusively to files:  
- `/Commands/{ServiceName}Service_Command.cs`  
- `/CommandHandlers/{ServiceName}CommandHandler.cs`

---

## ✅ Decision Points

### 1. Class Visibility (`{ServiceName}Service_Command`)

**Answer:** `public`

**Rationale:**  
Command classes are the entrypoint for Lore and potentially other factory components.  
They must be discoverable and instantiable across assembly boundaries. Keeping them `public` supports modular invocation and aligns with factory usage as part of an exposed execution surface.

---

### 7. Logging in `OrchestrateAsync` and Partials

**Answer:** Always use `ActionLog.Global.BeginStep(...)`  
**Rationale:** Guarantees scoped logging and traceable execution flow across all orchestrator and SRP methods. AI may infer nested `Step(...)` calls unless user specifies explicit steps in the design spec. Follow `ActionLogger.Factory.Usage.Standard.md`.

---

# 🧠 Service Class Design Decision Matrix (beta1)

## ✅ Additional Logging Decision Points

### X. Orchestrator Logging

**Answer:** Always begin with `ActionLog.Global.BeginStep(...)`  
**Rationale:** Establishes traceable execution entry for telemetry and debugging.

### X. SRP Partial Logging

**Answer:** Always begin with `ActionLog.Global.BeginStep(...)`  
**Rationale:** Ensures every unit of work is scoped and independently traceable.

### X. Nested Logging Steps

**Answer:** AI-inferred unless explicitly directed in spec  
**Rationale:** Allows flexibility while preserving traceability.

