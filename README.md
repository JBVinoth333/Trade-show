# Generators-Patterns Documentation Index

Welcome! This folder contains comprehensive documentation for understanding and creating API data generators with clear dependency mapping.

---

## 📚 Documentation Files

### 1. **QUICK_REFERENCE.md** ⭐ START HERE
**Best for**: Quick lookups, decision trees, cheat sheets
- At-a-glance type comparison
- Syntax reference table
- Common mistakes & fixes
- One-liner module dependencies
- Pro tips

**Read this when**: You need a quick answer or reference

### 2. **DATA_GENERATION_PATTERNS.md** 📖 COMPREHENSIVE GUIDE
**Best for**: Deep understanding and learning
- Complete explanation of Static vs Dynamic types
- Dependency models (linear, cross-namespace, chained)
- Reference patterns (5 different types)
- Real-world examples
- Testing dependencies checklist
- Common mistakes with solutions

**Read this when**: You want to understand the concepts fully

---

### 3. **DEPENDENCIES_MAP.md** 🗺️ VISUAL REFERENCE
**Best for**: Module relationships and hierarchy
- Portal module dependency hierarchy
- Support module dependency hierarchy
- Cross-module bridges (Portal ↔ Support)
- Independent foundation entities
- Recommended generation order (7 layers)
- Entity relationship model
- Quick lookup tables

**Read this when**: You need to understand which entities depend on which

---

### 4. **TEMPLATES_AND_EXAMPLES.md** 🛠️ PRACTICAL GUIDE
**Best for**: Copy-paste templates and real examples
- 7 reusable templates
- Decision tree for choosing template
- Complete practical example (Ticket creation)
- Naming conventions
- Testing checklist
- Debugging failed generators
- Common operations by module

**Read this when**: You're creating new generators

---

## 🎯 Quick Navigation by Task

### "I want to understand the basics"
1. Read: [QUICK_REFERENCE.md](QUICK_REFERENCE.md) (5 min)
2. Skim: [DATA_GENERATION_PATTERNS.md](DATA_GENERATION_PATTERNS.md) Section 1-2 (10 min)
3. Done! You understand Static vs Dynamic types

### "I need to create a new generator"
1. Check: [DEPENDENCIES_MAP.md](DEPENDENCIES_MAP.md) - What are the dependencies?
2. Use: [TEMPLATES_AND_EXAMPLES.md](TEMPLATES_AND_EXAMPLES.md) - Pick the right template
3. Reference: [QUICK_REFERENCE.md](QUICK_REFERENCE.md) - Verify syntax
4. Test: [TEMPLATES_AND_EXAMPLES.md](TEMPLATES_AND_EXAMPLES.md) Section "Testing Your Generator"

### "I'm debugging a broken generator"
1. Check: [TEMPLATES_AND_EXAMPLES.md](TEMPLATES_AND_EXAMPLES.md) - Debugging section
2. Verify: [QUICK_REFERENCE.md](QUICK_REFERENCE.md) - Common Mistakes table
3. Understand: [DATA_GENERATION_PATTERNS.md](DATA_GENERATION_PATTERNS.md) - Deep dive

### "I want to understand module relationships"
1. Study: [DEPENDENCIES_MAP.md](DEPENDENCIES_MAP.md) - Full hierarchy
2. Reference: [TEMPLATES_AND_EXAMPLES.md](TEMPLATES_AND_EXAMPLES.md) - Real examples
3. Trace: [DATA_GENERATION_PATTERNS.md](DATA_GENERATION_PATTERNS.md) - See patterns

### "I want the complete reference"
1. Read all 4 documents in order:
   - QUICK_REFERENCE.md (overview)
   - DATA_GENERATION_PATTERNS.md (concepts)
   - DEPENDENCIES_MAP.md (structure)
   - TEMPLATES_AND_EXAMPLES.md (practice)

---

## 🔑 Key Concepts Summary

### Generator Types
- **STATIC**: Hard-coded values (config, status, priority)
- **DYNAMIC**: API calls that extract and return data

### References
- **Generator ref**: `$generators:#/generators/name`
- **Output value**: `$name.value`
- **Input param**: `$.input.path:$.field`
- **Cross-module**: `../Module/test_data_generation_configurations.json#/generators/name`

### DataPath Pattern
- Format: `$.response.body:$.path.to.data[*].field`
- Used to extract specific data from API responses

### Dependency Levels
1. **Independent** - No dependencies (static values)
2. **Single-level** - Depends on one entity
3. **Multi-level** - Chain of dependencies (A→B→C)
4. **Cross-module** - Depends on entity in different module

---

## 📋 Common Scenarios

### Scenario 1: Simple Configuration
```
Need: Test data with fixed values
Files: QUICK_REFERENCE.md, TEMPLATES_AND_EXAMPLES.md Section "Template 1"
Time: 5 minutes
```

### Scenario 2: Entity Creation
```
Need: Create entity that depends on other entities
Files: DEPENDENCIES_MAP.md, TEMPLATES_AND_EXAMPLES.md Section "Template 2-3"
Time: 15 minutes
```

### Scenario 3: Complex Chain
```
Need: Multi-step dependency (A→B→C→D)
Files: TEMPLATES_AND_EXAMPLES.md Section "Template 3", DATA_GENERATION_PATTERNS.md Section 4
Time: 30 minutes

### Scenario 4: Cross-Module

Need: Entity depending on different module
Files: DEPENDENCIES_MAP.md, TEMPLATES_AND_EXAMPLES.md Section "Template 4"
Time: 20 minutes
```

### Scenario 5: Debugging

Need: Fix broken generator
Files: QUICK_REFERENCE.md (Common Mistakes), TEMPLATES_AND_EXAMPLES.md (Debugging)
Time: 10 minutes
```

---

## 🎓 Learning Path

### Level 1: Beginner (30 minutes)
- [ ] Read: QUICK_REFERENCE.md (Overview)
- [ ] Read: DATA_GENERATION_PATTERNS.md (Sections 1-2)
- [ ] Task: Identify dependencies for one entity

### Level 2: Intermediate (1 hour)
- [ ] Read: TEMPLATES_AND_EXAMPLES.md (Templates 1-3)
- [ ] Study: DEPENDENCIES_MAP.md (Module hierarchies)
- [ ] Task: Create a simple two-level generator

### Level 3: Advanced (2 hours)
- [ ] Read: All sections of all documents
- [ ] Study: TEMPLATES_AND_EXAMPLES.md (All templates)
- [ ] Task: Create complex multi-level generator with cross-module dependencies

### Level 4: Expert (Ongoing)
- [ ] Contribute: Add new examples as you discover patterns
- [ ] Optimize: Improve configurations for performance
- [ ] Document: Update docs when frameworks change

---

## 📖 Document Features

### QUICK_REFERENCE.md Features
✓ At-a-glance tables
✓ Decision trees
✓ Syntax quick lookup
✓ Common mistakes
✓ Summary formulas

### DATA_GENERATION_PATTERNS.md Features
✓ Detailed explanations
✓ Real-world examples
✓ Pattern analysis
✓ Best practices
✓ Troubleshooting guide

### DEPENDENCIES_MAP.md Features
✓ Visual hierarchies
✓ Module relationships
✓ Generation order (7 layers)
✓ Entity relationship diagrams
✓ Quick lookup tables

### TEMPLATES_AND_EXAMPLES.md Features
✓ Copy-paste templates (7 types)
✓ Decision tree for templates
✓ Complete practical example
✓ Naming conventions
✓ Testing & debugging

---

## 🚀 The Workflow

When you provide a requirement:
```
Your Input:
"I need test data for X with Y and Z relationships"

What I'll Do:
1. Identify dependencies (X needs Y, Y needs Z, Z is independent)
2. Choose appropriate templates from TEMPLATES_AND_EXAMPLES.md
3. Map module references from DEPENDENCIES_MAP.md
4. Verify syntax from QUICK_REFERENCE.md
5. Create test_data_generation_configurations.json
6. Verify no circular dependencies
7. Return working configuration

You'll Understand:
- Why each generator is needed
- What dependencies it has
- How to modify it in future
- How to create similar ones
```

---

## 💡 Tips for Success

1. **Start simple**: Begin with static generators before dynamic ones
2. **Map dependencies first**: Always identify what must exist before creating a new entity
3. **Use templates**: Don't write from scratch - use templates as starting point
4. **Set "name" property**: Enable reusability for chained generators
5. **Test incrementally**: Create one level at a time
6. **Reference these docs**: Bookmark this folder for future reference

---

## 🔗 Cross-References

### Understanding Static vs Dynamic?
→ See: DATA_GENERATION_PATTERNS.md Sections 1

### Need reference syntax?
→ See: QUICK_REFERENCE.md "Reference Syntax Quick Lookup"

### How to chain generators?
→ See: TEMPLATES_AND_EXAMPLES.md "Template 3"

### What depends on what?
→ See: DEPENDENCIES_MAP.md

### Common mistakes?
→ See: QUICK_REFERENCE.md "Common Mistakes & Fixes"

### Need a specific example?
→ See: TEMPLATES_AND_EXAMPLES.md "Complete Practical Example"

### Debugging issues?
→ See: TEMPLATES_AND_EXAMPLES.md "Debugging Failed Generators"

---

## 📞 Getting Help

### If you don't understand a concept:
1. Check the concept in QUICK_REFERENCE.md (table format)
2. Read detailed explanation in DATA_GENERATION_PATTERNS.md
3. See real example in TEMPLATES_AND_EXAMPLES.md

### If you need to create something:
1. Identify requirements
2. Check DEPENDENCIES_MAP.md for entity relationships
3. Choose template from TEMPLATES_AND_EXAMPLES.md
4. Use syntax from QUICK_REFERENCE.md
5. Verify against checklist in TEMPLATES_AND_EXAMPLES.md

### If something is broken:
1. Check syntax against QUICK_REFERENCE.md
2. Check common mistakes section
3. Follow debugging guide in TEMPLATES_AND_EXAMPLES.md
4. Check if dependencies are correct in DEPENDENCIES_MAP.md

---

## ✅ Checklist Before Using

- [ ] You understand Static vs Dynamic types
- [ ] You can identify dependencies for your entity
- [ ] You know which template to use
- [ ] You can write correct reference syntax
- [ ] You've checked for circular dependencies
- [ ] You've tested the configuration

---

## 📊 Statistics

| Aspect | Count |
|--------|-------|
| Portal Modules | ~35 |
| Support Modules | ~150+ |
| Generator Types | 2 (Static, Dynamic) |
| Reference Syntax Types | 4 |
| Templates Provided | 7 |
| Generation Layers | 7 |
| Common Mistakes Covered | 6 |

---

## 🎯 Remember

The key to understanding this system:

> **Every entity depends on something (or nothing if it's independent). Map those dependencies, then create generators to get the required data. That's it!**

---

## 📝 Version Info

- **Created**: February 2026
- **Based on Analysis**: api-data-generators with 150+ modules
- **Format**: Markdown with examples
- **Updated**: As patterns evolve

---

## 🎓 Final Notes

These documents provide everything you need to:
✓ Understand how generators work
✓ Create new generators correctly
✓ Debug broken generators
✓ Map entity relationships
✓ Plan test data generation

Bookmark this folder and refer back often. As you create more generators, these docs will become your quick reference guide.

**Good luck with your generators!** 🚀

---

**Start with**: [QUICK_REFERENCE.md](QUICK_REFERENCE.md) for a quick overview, then dive into other documents as needed.