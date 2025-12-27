# JLCPCB parts finder - Claude Code Skill

A Claude Code skill for searching the JLCPCB electronic components database.

## What Was Created

This skill replaces the old MCP server approach with a more efficient, on-demand skill:

1. **SKILL.md** - Instructions for Claude on how to use this skill
2. **query.js** - Node.js script to query the database
3. **package.json** - Dependencies (better-sqlite3)
4. **node_modules/** - Installed dependencies

## How It Works

When you invoke this skill in Claude Code (by typing `/jlcpcb` or similar), Claude will:
1. Read the instructions from `SKILL.md`
2. Use the `query.js` script to search the database
3. Present results in a clean, formatted way

## Advantages Over MCP Server

✅ **Token Efficient**: Only loaded when you need it
✅ **Simpler**: No background processes or complex setup
✅ **Flexible**: Easy to modify and maintain
✅ **Focused**: Only active for electronics projects

## Database Location

The skill queries the database at: `~/.jlcpcb-db/cache.sqlite3`

If you haven't downloaded it yet, visit: https://yaqwsx.github.io/jlcparts/

## Usage in Claude Code

### Invoke the skill
```
/jlcpcb
```

Then ask questions like:
- "Find me a 3.5mm audio jack"
- "What voltage regulators are available?"
- "Search for capacitors with keyword X"

### Direct script usage (for testing)

```bash
# List all categories
node ~/.claude/skills/jlcpcb/query.js list-categories

# Search for parts
node ~/.claude/skills/jlcpcb/query.js search-parts <category_id> [keyword] [limit]

# Examples
node ~/.claude/skills/jlcpcb/query.js search-parts 208 "3.5" 10
node ~/.claude/skills/jlcpcb/query.js search-parts 130 "" 20
```

## Available Commands

### list-categories
Lists all component categories with their IDs.

**Output format:** `ID: Category > Subcategory`

### search-parts
Searches for components within a category.

**Parameters:**
- `category_id` (required): Category ID number
- `keyword` (optional): Search term for manufacturer or description
- `limit` (optional): Maximum results (default: 20)

**Output format:** `C{lcsc}: {mfr} - {description} ({package}, Stock: {stock})`

## Common Categories

- **Audio Connectors**: 208
- **Linear Voltage Regulators**: 130
- **PMIC - Current & Power Monitors & Regulators**: 512
- **LDO Regulators**: 111, 120
- **Capacitors**: Various (use list-categories)
- **Resistors**: Various (use list-categories)

## Maintenance

### Updating the Skill

To modify the skill instructions, edit `skill.md`

To update the query logic, edit `query.js`

### Upgrading Dependencies

```bash
cd ~/.claude/skills/jlcpcb
npm update
```

## Migrating from MCP Server

The old MCP server can be removed:

```bash
# Remove MCP server from Claude config
claude mcp remove jlcpcb-mcp-parts-finder

# Optional: Uninstall the npm package
npm uninstall -g jlcpcb-mcp-parts-finder
```

## Troubleshooting

### Database not found
- Ensure `~/.jlcpcb-db/cache.sqlite3` exists
- Download from https://yaqwsx.github.io/jlcparts/

### Script errors
- Verify Node.js is installed: `node --version`
- Reinstall dependencies: `cd ~/.claude/skills/jlcpcb && npm install`

### Skill not appearing
- Restart Claude Code completely
- Check SKILL.md exists in `~/.claude/skills/jlcpcb/`

## File Structure

```
~/.claude/skills/jlcpcb/
├── SKILL.md           # Skill instructions for Claude
├── query.js           # Database query script
├── package.json       # Node.js dependencies
├── package-lock.json  # Locked dependency versions
├── node_modules/      # Installed packages
└── README.md          # This file
```

## License

MIT (same as the original MCP server)
