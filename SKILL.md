# JLCPCB parts finder

**Slash Command**: `/jlcpcb`

Search the JLCPCB electronic components database (~7 million parts) for hardware/electronics projects.

## Activation

This skill is activated when:
- User types `/jlcpcb` command
- User asks about finding electronic components for JLCPCB
- User mentions needing to search for parts, connectors, ICs, etc.

## When to Use This Skill

Use this skill when the user needs to:
- Find electronic components (resistors, capacitors, ICs, connectors, etc.)
- Look up specific part numbers or manufacturers
- Find alternatives or equivalents for components
- Check component availability and stock at JLCPCB
- Get component specifications (package type, description, etc.)

## Database Location

The JLCPCB database is located at: `~/.jlcpcb-db/cache.sqlite3`

If the database doesn't exist, inform the user they need to download it from https://yaqwsx.github.io/jlcparts/

## Available Operations

### 1. List Categories

Query all available component categories to find the right category ID.

**SQL Query:**
```sql
SELECT id, category, subcategory
FROM categories
ORDER BY category, subcategory
```

**Output format:** `ID: Category > Subcategory`

### 2. Search Parts

Search for components within a specific category.

**With keyword:**
```sql
SELECT lcsc, mfr, description, package, stock
FROM components
WHERE category_id = ? AND (mfr LIKE ? OR description LIKE ?)
ORDER BY stock DESC
LIMIT ?
```

**Without keyword:**
```sql
SELECT lcsc, mfr, description, package, stock
FROM components
WHERE category_id = ?
ORDER BY stock DESC
LIMIT ?
```

**Parameters:**
- `category_id` (required): Category ID number
- `keyword` (optional): Search term (use `%keyword%` for LIKE queries)
- `limit` (optional): Maximum results (default: 20)

**Output format:** `C{lcsc}: {mfr} - {description} ({package}, Stock: {stock})`

## Common Categories

Reference these common category IDs to help users quickly:

- **Voltage Regulators**: Category 512 (PMIC - Current & Power Monitors & Regulators)
- **Audio Connectors**: Category 208
- **Resistors**: Various categories (user should list_categories to find specific types)
- **Capacitors**: Various categories (user should list_categories to find specific types)

## How to Execute Queries

**IMPORTANT**: Use the helper script at `~/.claude/skills/jlcpcb/query.js` for all queries.

### List all categories:
```bash
node ~/.claude/skills/jlcpcb/query.js list-categories
```

### Search for parts:
```bash
node ~/.claude/skills/jlcpcb/query.js search-parts <category_id> [keyword] [limit]
```

**Examples:**
```bash
# Search for 3.5mm audio jacks
node ~/.claude/skills/jlcpcb/query.js search-parts 208 "3.5" 10

# List parts in a category
node ~/.claude/skills/jlcpcb/query.js search-parts 130 "" 20

# Find regulators with keyword
node ~/.claude/skills/jlcpcb/query.js search-parts 512 "LDO" 15
```

**Alternative**: You can also use sqlite3 CLI directly if needed:
```bash
sqlite3 ~/.jlcpcb-db/cache.sqlite3 "SELECT id, category, subcategory FROM categories LIMIT 10"
```

## Workflow

1. **User asks for a component** (e.g., "Find me a 12V linear regulator")
2. **Determine the category**:
   - If you know the category ID, proceed to search
   - If unsure, list categories to find the right one
3. **Search for parts** using the category_id and keyword
4. **Present results** clearly with part numbers, manufacturers, descriptions, packages, and stock

## Tips for Better Results

- Start with broader keywords if specific searches return no results
- Part numbers may be listed differently (e.g., "7812" vs "LM7812")
- LCSC part numbers start with "C" prefix (e.g., C123456)
- Stock is sorted in descending order to show most available parts first
- Limit results to 10-20 for initial searches to avoid overwhelming output

## Error Handling

If the database is not found at `~/.jlcpcb-db/cache.sqlite3`:
1. Inform the user the database is missing
2. Direct them to download from https://yaqwsx.github.io/jlcparts/
3. Provide installation instructions from the README

## Example Usage

**User:** "Find me a 3.3V LDO regulator"

**Your approach:**
1. Check if category 512 (PMIC/Regulators) is appropriate
2. Search with keyword "3.3V LDO"
3. Present top 10 results with part numbers and specs

**User:** "What audio jacks are available?"

**Your approach:**
1. List categories to confirm category 208 (Audio Connectors)
2. Search category 208 without keyword or with "jack"
3. Show results with descriptions and packages
