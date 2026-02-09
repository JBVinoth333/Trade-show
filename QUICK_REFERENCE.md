# Quick Reference Guide

## 🎯 At a Glance

### Two Main Types of Generators

| Type | What | When | Example |
|------|------|------|---------|
| **STATIC** | Hard-coded values | Configuration, enums, constants | `"status": ["Open", "Closed"]` |
| **DYNAMIC** | API call + extract data | Depend on other entities | `articleId = Solution.listAllArticles()` |

---

## 📊 The Dependency Pattern (Book Borrow Example)

```
BEFORE borrowing a book, you need:
┌─────────────────────────────────────────────┐
│ 1. BOOK (Static/Dynamic)                    │
│ 2. CUSTOMER (Static/Dynamic)                │
│ 3. LINK between them (Dynamic)              │
└─────────────────────────────────────────────┘

TICKET example:
┌─────────────────────────────────────────────┐
│ 1. DEPARTMENT (External dependency)         │
│ 2. AGENT (External dependency)              │
│ 3. STATUS (Static)                          │
│ 4. Create TICKET using all above            │
└─────────────────────────────────────────────┘
```

---

## 🔍 Generator Types at a Glance

### Static Generator
```json
{
  "type": "static",
  "value": ["choice1", "choice2", "choice3"]
}
```

### Dynamic Generator (Single Call)
```json
{
  "type": "dynamic",
  "generatorOperationId": "Module.operation",
  "dataPath": "$.response.body:$.data[*].id",
  "params": {
    "paramName": "value_or_reference"
  }
}
```

### Dynamic Generator (Chained - for dependencies)
```json
{
  "type": "dynamic",
  "generatorOperationId": "Module.operation",
  "dataPath": "$.response.body:$.data[*].id",
  "name": "reference_name",        // ← Set this so others can use: $reference_name.value
  "params": {
    "paramName": "$previous.value"  // ← Use output from previous generator
  }
}
```

---

## 🔗 Reference Syntax Quick Lookup

| Need | Syntax | Example |
|------|--------|---------|
| Reference another generator | `$generators:#/generators/name` | `"sortBy": "$generators:#/generators/sortBy_param"` |
| Use generator output value | `$name.value` | `"articleId": "$articles.value"` |
| Get from current request | `$.input.path:$.field` | `"entityId": "$.input.path:$.entityId"` |
| Extract from response | `$.response.body:$.path` | `"dataPath": "$.response.body:$.data[*].id"` |
| Cross-module reference | `../Module/...#/generators/` | `"../Agent/...#/generators/agent_id"` |

---

## 📁 Folder Organization

```
api-data-generators/
├── portal/          ← Customer-facing (Articles, Community, Comments)
│   ├── Solution/
│   ├── ArticleComment/
│   ├── ArticleFeedback/
│   └── ...
│
└── support/        ← Internal system (Tickets, Agents, Departments)
    ├── Ticket/
    ├── Agent/
    ├── Department/
    ├── Contact/
    └── ...
```

---

## ⚡ Quick Decision Tree

```
Question 1: Does this entity depend on anything?
├─ NO  → Use STATIC generator (config, status, priority, etc.)
└─ YES → Continue to Question 2

Question 2: How many dependencies?
├─ ONE dependency
│  ├─ Same module? → Use relative: #/generators/
│  └─ Different module? → Use path: ../Module/...#/generators/
│
└─ MULTIPLE dependencies (chain A→B→C)
   └─ Create chained: A with "name", then B uses $A.value, then C uses $B.value

Question 3: Need the current request's parameters?
├─ YES → Use: $.input.path:$.paramName
└─ NO  → Use: $generators:# or $name.value references

Question 4: Testing multiple scenarios (200, 422, 404)?
└─ YES → Create separate entries for each status code
```

---

## 🚀 Creating a New Generator (5 Steps)

### Step 1: Identify Dependencies
Ask: "What must exist before creating this entity?"
- Department must exist? ✓ Add to params
- Agent must exist? ✓ Add to params
- Is it just configuration? ✓ Use static

### Step 2: Classify Each Dependency
- Static (config) → `"type": "static"`
- External (other module) → `"../ModuleName/..."`
- Internal (same module) → `"#/generators/"`

### Step 3: Choose Template
- No deps → Template 1 (Static)
- 1 dep → Template 2 (Single Dynamic)
- Chain → Template 3 (Multi-level Dynamic)
- Multiple scenarios → Template 5 (Status codes)

### Step 4: Write Configuration
```json
{
  "apis": {
    "operationName": {
      "200": {
        "requiredParam1": "reference_to_dep1",
        "requiredParam2": "reference_to_dep2"
      }
    }
  },
  "generators": {
    "dependency1": [ /* define it */ ],
    "dependency2": [ /* define it */ ]
  }
}
```

### Step 5: Verify
- ✓ All references use correct syntax
- ✓ No circular dependencies
- ✓ Module paths are correct
- ✓ DataPath extracts correct field
- ✓ Chained generators have `"name"` property

---

## 🎓 Understanding DataPath

```
Format: $.response.body:$.path.to.data[*].field
        │────────────┬───────────│  └──────┬──────┘
        │            │          │         │
        └─ Where     ├─ Then    └─ Extract
          to find    └─ path

Examples:
$.response.body:$.data[*].id
  ├─ Go to response body
  ├─ Navigate to .data
  ├─ Get all items [*]
  └─ Extract .id from each

$.response.body:$.article.id
  ├─ Go to response body
  ├─ Navigate to .article
  └─ Extract single .id

$.response.body:$.items[0].metadata.value
  ├─ Go to response body
  ├─ Get first item [0]
  ├─ Navigate to .metadata
  └─ Extract .value
```

---

## 🔄 Dependency Hierarchy (Execution Order)

```
LAYER 1 - Foundation (No dependencies)
├─ Static values (status, priority, locale)
├─ Root entities (Department, Organization)
└─ System config (Role, Channel, BusinessHour)

LAYER 2 - Basic Entities
├─ Agent (depends on Department)
├─ Contact (usually independent)
└─ User (system user)

LAYER 3 - Compound Entities
├─ Ticket (depends on Agent + Department + Contact)
├─ Article (depends on Department)
└─ Task (depends on Contact or Ticket)

LAYER 4 - Related Entities
├─ TicketComment (depends on Ticket + Agent)
├─ ArticleComment (depends on Article)
└─ Attachment (depends on Ticket or Article)

LAYER 5 - Complex Relationships
├─ Automation (depends on Ticket + conditions)
├─ Report (depends on Ticket data)
└─ Dashboard (depends on Report)
```

---

## ⚠️ Common Mistakes & Fixes

| Problem | Wrong | Right |
|---------|-------|-------|
| **DataPath format** | `$.data.id` | `$.response.body:$.data[*].id` |
| **Module path** | `../Agent/` (exists as Agent folder) | `../Agent/test_data_generation_configurations.json` |
| **Generator ref** | `sortBy_param` | `$generators:#/generators/sortBy_param` |
| **Output value** | `$article` | `$article.value` (if "name": "article") |
| **Circular dep** | A→B→C→A | Break cycle with static value |
| **Chained missing name** | No "name" in first generator | Add `"name": "reference_name"` |

---

## 📋 Checklist for New Configuration

Before using a new generator configuration:

```
□ Syntax is valid JSON
□ All module references exist (../Module names are correct)
□ DataPath uses correct format ($.response.body:...)
□ Generator names are set for chained generators
□ No circular dependencies (A depends on B, B depends on A)
□ Required parameters are in all applicable status codes
□ Static values match system's actual values
□ Cross-module dependencies are documented
□ Naming follows pattern: operation_param
```

---

## 🎬 Common Workflows

### Workflow 1: Simple Test Data Creation
```
"I need a Ticket with status=Open"

Answer:
├─ Create static generator for "ticket_status": ["Open"]
├─ Get department_id from ../Department/...
├─ Get agent_id from ../Agent/...
├─ Get contact_id from Contact.listAllContacts()
└─ Create ticket with all above
```

### Workflow 2: Nested Entity Creation
```
"I need an ArticleComment with feedback"

Answer:
├─ Step 1: Get articleId (Solution.listAllArticles)
├─ Step 2: Add comment using articleId (ArticleComment.add)
├─ Step 3: Add feedback using articleId + commentId
└─ Set "name" in Step 1 & 2 for reference in Step 3
```

### Workflow 3: Multi-Department Setup
```
"I need Tickets across different departments"

Answer:
├─ Get multiple department IDs (dataPath: $.data[*].id)
├─ Get agent IDs filtered by each department
├─ Create tickets with appropriate dept + agent pairs
└─ Use loop/array handling in params
```

---

## 🔍 Module Map (One-liner Dependencies)

```
Ticket        → Agent, Department, Contact (external)
TicketComment → Ticket, Agent (external)
Article       → Product (optional), Department (optional)
ArticleComment→ Article (external)
Contact       → Account (optional)
Task          → Contact or Ticket (external)
Agent         → Department (external)
Community     → Independent (mostly)
Thread        → Community (external)
```

---

## 📞 When to Ask for Help

I can automatically determine dependencies and create correct configurations when you tell me:

✓ **"I need test data for [Entity] with [Relationships]"**
```
Example: "I need test data for Ticket with Agent, Department, and Contact"
```

✓ **"Create an API sequence: A → B → C"**
```
Example: "Create sequence: Create Department → Create Agent → Create Ticket"
```

✓ **"Add generator for [Module]"**
```
Example: "Add a generator for TaskTimer that needs Task, Agent, and startTime"
```

---

## 📚 Files in Generators-Patterns Folder

1. **DATA_GENERATION_PATTERNS.md** (This manual)
   - Full concepts and theory
   - Understanding types and dependencies
   - Real-world examples

2. **DEPENDENCIES_MAP.md**
   - Visual hierarchy of all modules
   - Which entities depend on which
   - Recommended generation order
   - Quick lookup table

3. **TEMPLATES_AND_EXAMPLES.md**
   - Copy-paste templates
   - Practical examples
   - Decision tree
   - Debugging guide

4. **QUICK_REFERENCE.md** (This file)
   - Cheat sheet format
   - Quick lookups
   - Common mistakes
   - Decision trees

---

## 🎯 Formula for Any Generator

```
New Generator = 
  ✓ Identify what must exist first
  + ✓ Get those IDs using dynamic generators  
  + ✓ Combine with static configuration
  + ✓ Extract response data using dataPath
  = ✓ Ready to use!
```

---

## 💡 Pro Tips

1. **Always set `"name"` in dynamic generators** that will be reused
   - Makes chaining easier: `"paramId": "$name.value"`

2. **Use multiple status codes** (at least 200 and 422)
   - Tests both success and validation paths

3. **Group related generators** by operation name
   - `operation_param1`, `operation_param2`, etc.
   - Makes configs easier to navigate

4. **Test dataPath extraction** by checking actual API responses
   - Wrong path = wrong or empty data

5. **Document complex chains** with comments
   - Future you will thank current you

6. **Reuse existing generators** before creating new ones
   - Check if Department already has what you need

---

## 🚦 Summary

| Concept | Remember | Used For |
|---------|----------|----------|
| Static | Hard-coded values | Config, status, priority |
| Dynamic | API call output | Entity IDs, real data |
| $generators:# | Reference another generator | Parameters |
| $name.value | Use previous output | Chaining generators |
| $.response.body: | Extract from response | DataPath |
| ../ | Cross-module reference | External dependencies |
| "name" property | Enable reuse | Chaining generators |

---

## Next Steps

You now have:
✅ Complete understanding of generator patterns
✅ Visual dependencies map
✅ Copy-paste templates
✅ This quick reference

**Ready?** 
Give me any requirement like:
> "I need test data for a Ticket with comments from multiple agents in different departments"

And I'll create the exact configuration automatically! 🚀
