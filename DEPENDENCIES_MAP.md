# Generator Dependencies Map

## Portal Module Dependencies

```
PORTAL HIERARCHY:
================

ArticleFeedback
├── Depends On: Solution (articleId)
└── Depends On: Solution (locale)

ArticleComment
├── Depends On: Solution (articleId)
└── Depends On: Agent (optional - commenter)

Solution/Article
├── Depends On: Category (categoryId - optional)
└── Depends On: Department (searchAllArticles_departmentId)

Category & KbCategory
└── Independent (Static values only)

Comment
├── Depends On: Community (entityId)
└── Depends On: User (optional - commenter)

Community & CommunityUser
├── Independent or has self-references
└── May depend on Department

Thread
└── Depends On: Community (threadId)

Topic
└── May depend on Category or KbCategory
```

---

## Support Module Dependencies

```
SUPPORT HIERARCHY:
==================

Ticket (Core Entity)
├── Depends On: Agent (agentIds)
├── Depends On: Department (departmentId)
├── Depends On: Contact (contactId - requester)
└── Depends On: Product (optional)

TicketComment
├── Depends On: Ticket (ticketId)
└── Depends On: Agent (agentId - commenter)

Agent (Foundation)
├── Depends On: Department (departmentId)
├── Depends On: User (userId)
└── Depends On: Team (optional)

Department (Foundation)
├── Depends On: Organization (optional)
└── Independent Mostly

Contact (Foundation)
├── Depends On: Account (optional)
└── Independent Mostly

Article (Support KnowledgeBase)
├── Depends On: Product (optional)
└── Depends On: Department (optional)

Task/Event
├── Depends On: Ticket or Contact (entityId)
└── Depends On: Agent (assignee)

Call
├── Depends On: Agent (agentId)
├── Depends On: Contact (contactId)
└── Depends On: Department (departmentId)

TicketAttachment
└── Depends On: Ticket (ticketId)

Automation/Blueprint
├── Depends On: Criteria (conditions)
└── Depends On: Department (optional)

SLA
└── Depends On: Department (departmentId)

CustomView/SavedFilters
├── Depends On: Department (optional)
└── Depends On: Ticket (optional - for filters)

Role
└── Independent (Static system roles)

Skill & SkillConfiguration
├── Depends On: Agent (optional - for assignment)
└── Independent configuration

BusinessHour
└── Independent (Organization-level setting)

Channel & Integration
└── Independent (System-level configuration)
```

---

## Cross-Module Bridges

```
PORTAL ← → SUPPORT Connections:
==================================

Portal needs Support data:
┌─────────────────────────────────────┐
│ ArticleFeedback.addFeedback needs   │
│   └─ articleId from Solution        │
│   └─ locale (static)                │
└─────────────────────────────────────┘

┌─────────────────────────────────────┐
│ Article (Portal) references         │
│   └─ May link to Article (Support)  │
│   └─ via Product (shared)           │
└─────────────────────────────────────┘

┌─────────────────────────────────────┐
│ Community interactions in Portal    │
│   └─ May reference Agents (Support) │
│   └─ in specialized workflows       │
└─────────────────────────────────────┘
```

---

## Independent Foundation Entities

These entities have NO external dependencies:

```
ALWAYS AVAILABLE (Can create anytime):
======================================

STATIC GENERATORS:
- Locales (en, es, fr, etc.)
- Statuses (Open, Closed, In Progress, etc.)
- Priorities (High, Medium, Low)
- Permissions (Read, Write, Admin, etc.)
- Sort parameters (createdTime, updatedTime, etc.)
- System roles and permissions

MINIMAL DEPENDENCY:
- Department (root-level, may only need Organization)
- Role (system-defined)
- Channel (system configuration)
- Team (simple reference)
```

---

## Generation Order (Recommended Sequence)

```
LAYER 1 - Foundation (No dependencies):
├── Department
├── Role
├── BusinessHour
├── Channel
└── All Static Generators

LAYER 2 - User entities:
├── Agent (depends on Department from Layer 1)
├── User (independent or minimal dependency)
├── Team (depends on Agent)
└── Skill

LAYER 3 - Base entities:
├── Contact (independent)
├── Account (independent)
├── Product (independent)
└── Ticket (depends on Agent, Department, Contact from Layer 1-2)

LAYER 4 - Relationship entities:
├── TicketComment (depends on Ticket from Layer 3)
├── Task (depends on Agent, Contact, Ticket)
├── Call (depends on Agent, Contact, Department)
├── Article (depends on Product, Department)
└── ArticleComment (depends on Article)

LAYER 5 - Complex entities:
├── Automation (depends on Ticket, Agent, Department)
├── CustomView (depends on Ticket, saved filters)
├── Report (depends on Ticket, Agent, Department)
└── Dashboard (depends on Report, metrics)

LAYER 6 - Portal entities:
├── Community (independent or light dependency)
├── ArticleFeedback (depends on Article from Layer 4)
├── Thread (depends on Community)
└── Topic (depends on Category)
```

---

## Entity Relationship Model

### One-to-Many Relationships
```
Department ──[1:N]──> Ticket
Department ──[1:N]──> Agent
Department ──[1:N]──> SLA

Agent ──[1:N]──> Ticket (assignee)
Agent ──[1:N]──> TicketComment (commenter)

Contact ──[1:N]──> Ticket (requester)
Contact ──[1:N]──> Activity

Article ──[1:N]──> ArticleComment
Article ──[1:N]──> ArticleFeedback

Community ──[1:N]──> Thread
Community ──[1:N]──> CommunityUser
```

### Many-to-Many Relationships
```
Agent ──[N:N]──> Skill
Contact ──[N:N]──> Tag
Ticket ──[N:N]──> CustomField

User ──[N:N]──> Role
```

### Self-References
```
Article ──> ArticleVersion (previous version)
Ticket ──> Ticket (related/linked tickets)
Category ──> Category (parent category)
```

---

## Dependency Severity Levels

### CRITICAL (Must exist first)
- ✓ Agent before creating Ticket
- ✓ Department before creating Agent  
- ✓ Contact before creating Ticket
- ✓ Article before creating ArticleComment
- ✓ Ticket before creating TicketComment

### HIGH (Strongly recommended)
- ✓ Product before creating Article (though optional)
- ✓ Skill before assigning to Agent
- ✓ SLA before Ticket automation

### MEDIUM (Enhances test data)
- ✓ Tag for Article categorization
- ✓ CustomField for Ticket enrichment
- ✓ Template for consistent creation

### LOW (Optional enrichment)
- ✓ Attachment to Ticket
- ✓ Follower relationship
- ✓ Custom properties

---

## Parameter Type Classification

### Always Required
```json
{
  "type": "required",
  "examples": ["ticketId", "departmentId", "agentId", "articleId"]
}
```

### Sometimes Required (Conditional)
```json
{
  "type": "conditional",
  "examples": ["categoryId (if filtering)", "departmentId (if cross-department)"]
}
```

### Optional
```json
{
  "type": "optional",
  "examples": ["tags[]", "customFields", "attachments", "followers"]
}
```

---

## Quick Decision Tree

```
When creating test data for Entity X:

1. Is X a root entity? (Department, Account, etc.)
   YES → Use static generators
   NO → Continue to step 2

2. What entities must exist before X?
   → List all dependencies (check Dependency Map above)

3. Are dependencies in same module or different?
   Same → Use relative path: ../ModuleName/...
   Different → Use relative path: ../ModuleName/...

4. Are there multiple levels of dependencies?
   YES → Create chained generators with names
   NO → Create single dynamic generator

5. What data do you need from dependencies?
   → IDs typically
   → Use dataPath to extract: $.response.body:$.data[*].id

6. Ready to create generator!
   → Follow pattern from DATA_GENERATION_PATTERNS.md Section 9
```

---

## Common Patterns Summary

| Pattern | Use Case | Example |
|---------|----------|---------|
| **Static** | Configurations, constants | Status values, Permissions |
| **Dynamic Single-Level** | Simple dependency | Article needs Category |
| **Dynamic Multi-Level** | Chained dependency | Comment needs Article which needs Category |
| **Cross-Module** | Dependencies across Portal/Support | Article needs Department |
| **Array Extraction** | Get all items | `$.data[*].id` gets all IDs |
| **Conditional Ref** | Use based on condition | `$param.value` or static fallback |

---

## Troubleshooting Dependency Issues

### Issue: Generator references unknown module
**Solution**: Verify module exists in api-data-generators folder structure

### Issue: dataPath returns empty or wrong data
**Solution**: 
1. Check actual API response structure
2. Verify JSON path syntax: `$.response.body:$.path[*].id`
3. Test path extraction

### Issue: Circular dependency detected
**Solution**: 
1. Identify the cycle: A→B→C→A
2. Break cycle by using static value for one dependency

### Issue: Child entity can't find parent
**Solution**:
1. Ensure parent generator has `"name"` property
2. Reference it correctly: `$name.value`
3. Verify parent executes before child (order matters)

---

## Reference Quick Links

- **Portal Modules**: ArticleFeedback, ArticleComment, Community, Thread, Topic, Solution, Category
- **Support Modules**: Ticket, Agent, Department, Contact, Task, Call, Article, CustomView
- **Foundation Modules**: User, Role, Organization, Channel, Team, Skill
- **Configuration**: BusinessHour, SLA, TemplateFolder, Layout, ValidationRules

---

## Future Expansion

When adding new modules:
1. Identify if it's independent or dependent
2. Map all incoming dependencies (what it needs)
3. Map all outgoing dependencies (what needs it)
4. Update this document with new layer if needed
5. Update Generation Order sequence
