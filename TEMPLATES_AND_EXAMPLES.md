# Generator Templates & Practical Examples

## Template 1: Simple Static Generator

**When to use**: Constants, configurations, enumerations

```json
{
  "generators": {
    "generator_name": [
      {
        "type": "static",
        "value": ["value1", "value2", "value3"]
      }
    ]
  }
}
```

**Real Example**:
```json
{
  "generators": {
    "ticket_status": [
      {
        "type": "static",
        "value": ["Open", "In Progress", "Resolved", "Closed"]
      }
    ],
    "ticket_priority": [
      {
        "type": "static",
        "value": ["High", "Medium", "Low"]
      }
    ]
  }
}
```

---

## Template 2: Single-Level Dynamic Generator

**When to use**: Creating data that depends on one existing entity

```json
{
  "generators": {
    "dependency_id": [
      {
        "type": "static",
        "value": ["some_static_param"]
      }
    ],
    "new_entity_id": [
      {
        "type": "dynamic",
        "generatorOperationId": "Module.Operation",
        "dataPath": "$.response.body:$.data[*].id",
        "params": {
          "requiredParam": "$generators:#/generators/dependency_id"
        }
      }
    ]
  }
}
```

**Real Example**:
```json
{
  "generators": {
    "listAllArticles_sortBy": [
      {
        "type": "static",
        "value": ["createdTime"]
      }
    ],
    "articleId": [
      {
        "type": "dynamic",
        "generatorOperationId": "Solution.listAllArticles",
        "dataPath": "$.response.body:$.data[*].id",
        "params": {
          "sortBy": "$generators:#/generators/listAllArticles_sortBy"
        }
      }
    ]
  }
}
```

---

## Template 3: Multi-Level Chained Dynamic Generator

**When to use**: Creating data through a chain of API calls

```json
{
  "generators": {
    // Level 1: Static configuration
    "param_1": [
      {
        "type": "static",
        "value": ["config_value"]
      }
    ],
    
    // Level 2: Dynamic - uses Level 1
    "entity_1_id": [
      {
        "type": "dynamic",
        "generatorOperationId": "Module1.Operation1",
        "dataPath": "$.response.body:$.data[*].id",
        "name": "entity1",
        "params": {
          "param": "$generators:#/generators/param_1"
        }
      }
    ],
    
    // Level 3: Dynamic - uses Level 2
    "entity_2_id": [
      {
        "type": "dynamic",
        "generatorOperationId": "Module2.Operation2",
        "dataPath": "$.response.body:$.id",
        "name": "entity2",
        "params": {
          "entityId": "$entity1.value"
        }
      }
    ],
    
    // Level 4: Dynamic - uses Level 3
    "entity_3_id": [
      {
        "type": "dynamic",
        "generatorOperationId": "Module3.Operation3",
        "dataPath": "$.response.body:$.id",
        "params": {
          "entity2Id": "$entity2.value",
          "staticParam": "$generators:#/generators/param_1"
        }
      }
    ]
  }
}
```

**Real Example - Article Feedback Comment Chain**:
```json
{
  "generators": {
    // Step 1: Static
    "sortBy": [
      {
        "type": "static",
        "value": ["createdTime"]
      }
    ],
    
    // Step 2: Get articles
    "articleId": [
      {
        "type": "dynamic",
        "generatorOperationId": "Solution.listAllArticles",
        "dataPath": "$.response.body:$.data[*].id",
        "name": "articles",
        "params": {
          "sortBy": "$generators:#/generators/sortBy"
        }
      }
    ],
    
    // Step 3: Add comment to article
    "commentId": [
      {
        "type": "dynamic",
        "generatorOperationId": "ArticleComment.addArticleTranslationComment",
        "dataPath": "$.response.body:$.id",
        "name": "comments",
        "params": {
          "articleId": "$articles.value"
        }
      }
    ],
    
    // Step 4: Add feedback with comment
    "feedbackId": [
      {
        "type": "dynamic",
        "generatorOperationId": "ArticleFeedback.addFeedback",
        "dataPath": "$.response.body:$.id",
        "params": {
          "articleId": "$articles.value",
          "commentId": "$comments.value"
        }
      }
    ]
  }
}
```

---

## Template 4: Cross-Module Dependency

**When to use**: Entity depends on something from another module

```json
{
  "apis": {
    "createEntity": {
      "200": {
        "localDependencyId": "#/generators/local_dependency",
        "crossModuleDependencyId": "../OtherModule/test_data_generation_configurations.json#/generators/other_generator"
      }
    }
  },
  "generators": {
    "local_dependency": [
      {
        "type": "static",
        "value": ["value"]
      }
    ]
  }
}
```

**Real Example - Ticket with Agent and Department**:
```json
{
  "apis": {
    "createTicket": {
      "200": {
        "agentIds[*]": "../Agent/test_data_generation_configurations.json#/generators/agent_id",
        "departmentId": "../Department/test_data_generation_configurations.json#/generators/department_id",
        "contactId": "#/generators/contact_id",
        "status": "#/generators/ticket_status"
      }
    }
  },
  "generators": {
    "contact_id": [
      {
        "type": "dynamic",
        "generatorOperationId": "Contact.listAllContacts",
        "dataPath": "$.response.body:$.data[*].id"
      }
    ],
    "ticket_status": [
      {
        "type": "static",
        "value": ["Open"]
      }
    ]
  }
}
```

---

## Template 5: Multiple Status Code Handlers

**When to use**: Same API with different response scenarios

```json
{
  "apis": {
    "operationName": {
      "200": {
        "param1": "generator_ref_success",
        "param2": "generator_ref_success"
      },
      "422": {
        "param1": "generator_ref_validation",
        "param2": "generator_ref_validation"
      },
      "404": {
        "param1": "generator_ref_notfound"
      }
    }
  }
}
```

**Real Example - Ticket Operations**:
```json
{
  "apis": {
    "moveToTrash": {
      "422": {
        "ticketIds[*]": "#/generators/ticket_id"
      },
      "204": {
        "ticketIds[*]": "#/generators/ticket_id"
      }
    },
    "getCollidingUsersAndChatStatus": {
      "200": {
        "ticketId": "#/generators/ticket_id"
      }
    }
  }
}
```

---

## Template 6: Using Input Path Parameters

**When to use**: Need to extract data from current request parameters

```json
{
  "generators": {
    "dependency_from_input": [
      {
        "type": "dynamic",
        "generatorOperationId": "Module.Operation",
        "dataPath": "$.response.body:$.id",
        "params": {
          "parentId": "$.input.path:$.entityId"  // Get from current request path
        }
      }
    ]
  }
}
```

**Real Example - Article Comment extraction**:
```json
{
  "generators": {
    "articleCommentsById_commentId": [
      {
        "type": "dynamic",
        "generatorOperationId": "portal.ArticleComment.addArticleTranslationComment",
        "dataPath": "$.response.body:$.id",
        "params": {
          "articleId": "$.input.path:$.entityId"  // Article ID from the calling API's path
        }
      }
    ]
  }
}
```

---

## Template 7: Optional Parameters with Conditional Values

**When to use**: Some parameters may be optional or conditionally required

```json
{
  "generators": {
    "static_param": [
      {
        "type": "static",
        "value": ["primary_value"]
      }
    ],
    "conditional_dynamic": [
      {
        "type": "dynamic",
        "generatorOperationId": "Module.Operation",
        "dataPath": "$.response.body:$.data[*].id",
        "params": {
          "requiredParam": "$generators:#/generators/static_param",
          "optionalParam": "$generators:#/generators/another_generator"
        }
      }
    ]
  }
}
```

---

## Decision Tree: Which Template to Use?

```
Start with Question: What are the dependencies?

    ├─ NO dependencies? (Constants, static values)
    │  └─ Use: TEMPLATE 1 (Static Generator)
    │
    ├─ ONE dependency? (1 other entity needed)
    │  ├─ From same module?
    │  │  └─ Use: TEMPLATE 2 (Single-Level Dynamic)
    │  └─ From different module?
    │     └─ Use: TEMPLATE 4 (Cross-Module)
    │
    ├─ MULTIPLE sequential dependencies? (Chain A→B→C)
    │  └─ Use: TEMPLATE 3 (Chained Dynamic)
    │
    ├─ Need to test multiple scenarios? (200, 422, 404)
    │  └─ Use: TEMPLATE 5 (Multiple Status Codes)
    │
    ├─ Need current request's parameters?
    │  └─ Use: TEMPLATE 6 (Input Path)
    │
    └─ Optional parameters involved?
       └─ Use: TEMPLATE 7 (Conditional Values)
```

---

## Complete Practical Example: Ticket Creation

**Scenario**: Create a ticket with all required and optional dependencies

### Step 1: Understand Dependencies
```
Ticket needs:
- department (required, external)
- agent (required, external)  
- contact (required, internal)
- status (static)
- priority (static)
- tags (optional, internal)
```

### Step 2: Create Configuration
```json
{
  "apis": {
    "createTicket": {
      "200": {
        "departmentId": "../Department/test_data_generation_configurations.json#/generators/department_id",
        "agentIds[*]": "../Agent/test_data_generation_configurations.json#/generators/agent_id",
        "contactId": "#/generators/contact_id",
        "status": "#/generators/ticket_status",
        "priority": "#/generators/ticket_priority",
        "tags[*]": "#/generators/ticket_tags"
      },
      "422": {
        "departmentId": "../Department/test_data_generation_configurations.json#/generators/department_id",
        "agentIds[*]": "../Agent/test_data_generation_configurations.json#/generators/agent_id",
        "status": "#/generators/ticket_status"
      }
    }
  },
  "generators": {
    "contact_id": [
      {
        "type": "dynamic",
        "generatorOperationId": "Contact.listAllContacts",
        "dataPath": "$.response.body:$.data[*].id",
        "name": "contacts"
      }
    ],
    "ticket_status": [
      {
        "type": "static",
        "value": ["Open", "In Progress", "Resolved", "Closed"]
      }
    ],
    "ticket_priority": [
      {
        "type": "static",
        "value": ["High", "Medium", "Low"]
      }
    ],
    "ticket_tags": [
      {
        "type": "static",
        "value": ["urgent", "important", "followup"]
      }
    ]
  }
}
```

### Step 3: Execution Order (What framework will do)
```
1. Load Department dependencies (from ../Department/...)
2. Load Agent dependencies (from ../Agent/...)
3. Execute contact_id generator → Call Contact.listAllContacts
4. Execute ticket_status generator → Return ["Open", "In Progress", ...]
5. Execute ticket_priority generator → Return ["High", "Medium", "Low"]
6. Execute ticket_tags generator → Return ["urgent", "important", "followup"]
7. Call createTicket API with all gathered data
```

---

## Common Generator Naming Conventions

To make configurations easier to understand:

```
Pattern: {operationName}_{parameterName}_{modifier}

Examples:
- listAllArticles_sortBy        (sort parameter for listAllArticles)
- createTicket_status           (status parameter for createTicket)
- moveToDepartment_forumId      (forumId parameter for moveToDepartment)
- searchByTag_categoryId         (categoryId parameter for searchByTag)
- addFeedback_listAllArticles_sortBy  (nested: sortBy for listAllArticles used in addFeedback)

Benefits:
✓ Clear what operation uses this generator
✓ Easy to find related generators
✓ Reduces naming conflicts
✓ Makes dependencies obvious
```

---

## Testing Your Generator

### Before committing, verify:

```
□ All dependencies are correctly referenced
  └ Check ../Module names match actual folders

□ DataPath syntax is correct
  └ Format: $.response.body:$.path.to.data[*].field

□ Generator names are set for chained dependencies
  └ Each level that feeds another should have "name" property

□ Static values make sense
  └ Status values match actual system statuses

□ Cross-module references use correct paths
  └ ../AgentModule/... not ../Agent/...

□ Multiple status codes are handled
  └ At least 200 and 422 for most APIs

□ Required parameters are in all status handlers
  └ Optional ones can be in specific statuses

□ No circular dependencies
  └ A→B→C→A pattern should not exist
```

---

## Quick Reference: Common Operations by Module

### Solution/Article Operations
```
- listAllArticles       → Get all articles
- retriveAnArticle      → Get single article
- articleComments       → Get comments
- searchAllArticles     → Search with filters
```

### Agent Operations
```
- getAgents             → List all agents
- getAgentById          → Get single agent
- listAgentsByDept      → Filter by department
```

### Department Operations
```
- getDepartments        → List all departments
- getDepartmentById     → Get single department
```

### Contact Operations
```
- listAllContacts       → Get all contacts
- getContactById        → Get single contact
- searchContacts        → Search contacts
```

### Ticket Operations
```
- createTicket          → Create new ticket
- getTicket             → Get single ticket
- moveToTrash           → Move ticket to trash
- moveToDepartment      → Transfer between departments
```

---

## Debugging Failed Generators

### Issue: Generator returns empty array
**Check**:
1. Is the API actually returning data?
2. Is dataPath pointing to correct location?
3. Is the parent generator executed first?

**Solution**:
```json
// Wrong
"dataPath": "$.data[*].id"

// Correct
"dataPath": "$.response.body:$.data[*].id"
```

### Issue: Generator can't find referenced parameter
**Check**:
1. Is generator name spelled correctly?
2. Is "$" prefix used?
3. Is reference in correct format?

**Solution**:
```json
// Wrong
"params": { "sortBy": "sortBy_generator" }

// Correct
"params": { "sortBy": "$generators:#/generators/sortBy_generator" }
```

### Issue: Cross-module reference not found
**Check**:
1. Module folder name is correct
2. Path separator is "/" not "\"
3. Generator name exists in target module

**Solution**:
```json
// Wrong
"../DepartmentModule/test_data_generation_configurations.json#/generators/..."

// Correct
"../Department/test_data_generation_configurations.json#/generators/..."
```

---

## Next Steps for Your Scenarios

Once you provide a single prompt like:
> "I need to generate test data for X with relationships Y and Z"

I will:
1. ✅ Identify which template applies
2. ✅ Map all dependencies 
3. ✅ Determine which modules are needed
4. ✅ Check what already exists vs what needs creation
5. ✅ Generate correct test_data_generation_configurations.json
6. ✅ Verify no circular dependencies
7. ✅ Return ready-to-use configuration

With this document as reference, you'll understand exactly what I'm creating and why!
