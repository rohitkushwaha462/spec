# TOON Examples

This directory contains example TOON files demonstrating various features of the format. Examples are organized into three categories:

## Valid Examples

Complete, valid TOON files demonstrating core features:

### Objects

- [`valid/objects.toon`](valid/objects.toon) - Simple flat object with primitive values
  - Demonstrates basic key-value pairs
  - Shows multiple data types: string, number, boolean, null
  - Spec: §6 Objects

- [`valid/nested-objects.toon`](valid/nested-objects.toon) - Multi-level nested objects
  - Demonstrates indentation-based nesting (2 spaces per level)
  - Shows how objects can contain other objects
  - Spec: §6 Objects, §4 Indentation

### Arrays

- [`valid/primitive-arrays.toon`](valid/primitive-arrays.toon) - Inline primitive arrays
  - Demonstrates compact inline format `[N]: item1,item2,item3`
  - Shows empty arrays `[0]:`
  - Multiple arrays in one document
  - Spec: §7.1 Primitive Arrays

- [`valid/tabular-array.toon`](valid/tabular-array.toon) - Tabular array format (comma delimiter)
  - Demonstrates the most token-efficient format for uniform data
  - Header declares fields once: `{field1,field2,...}`
  - Rows contain only data values
  - Spec: §7.2 Tabular Arrays

- [`valid/mixed-array.toon`](valid/mixed-array.toon) - Mixed-type array (list format)
  - Demonstrates list format with `-` prefix
  - Shows arrays containing different types (number, object, string)
  - Useful when items don't have uniform structure
  - Spec: §7.3 Mixed Arrays

### Delimiters

- [`valid/pipe-delimiter.toon`](valid/pipe-delimiter.toon) - Pipe delimiter (`|`)
  - Shows alternative delimiter for tabular arrays
  - Delimiter marker appears in both header `[N|]` and field list `{field|...}`
  - Useful when data contains commas
  - Spec: §8 Delimiters

- [`valid/tab-delimiter.toon`](valid/tab-delimiter.toon) - Tab delimiter (`\t`)
  - Demonstrates tab-separated tabular format
  - Tab character appears in both header and between fields
  - Useful for TSV-like data
  - Spec: §8 Delimiters

### Key Folding and Path Expansion (v1.5+)

- [`valid/key-folding-basic.toon`](valid/key-folding-basic.toon) - Basic dotted-key notation
  - Demonstrates collapsing nested objects: `server.host: localhost` instead of 3 indentation levels
  - Significant token savings for deeply nested configuration data
  - Requires encoder option `keyFolding="safe"` to produce; decoder expands with `expandPaths="safe"`
  - Spec: §13.4 Key Folding and Path Expansion

- [`valid/key-folding-with-array.toon`](valid/key-folding-with-array.toon) - Dotted keys with arrays
  - Shows folding combined with inline arrays: `data.meta.items[3]: x,y,z`
  - Demonstrates that folding stops at array boundaries (arrays terminate chains)
  - Useful for API responses and structured data with nested metadata
  - Spec: §13.4 Key Folding

- [`valid/key-folding-mixed.toon`](valid/key-folding-mixed.toon) - Mixed folding strategies
  - Shows both folded (`app.name: MyApp`) and regular nested keys in same document
  - Demonstrates `flattenDepth` control for partial folding
  - Allows selective use of dotted notation where most beneficial
  - Spec: §13.4 Key Folding

- [`valid/path-expansion-merge.toon`](valid/path-expansion-merge.toon) - Deep merge behavior
  - Demonstrates how overlapping dotted keys merge during expansion
  - Shows `user.profile.name: Ada` + `user.settings.theme: dark` → nested object with both branches
  - Decoder option `expandPaths="safe"` reconstructs nested structure
  - Spec: §13.4 Path Expansion

## Invalid Examples

Examples that intentionally violate TOON syntax rules:

- **[`invalid/length-mismatch.toon`](invalid/length-mismatch.toon)** - Array length mismatch
  - Declares `[3]` but provides only 2 items
  - Should fail validation in strict mode
  - Spec: §9 Validation

- **[`invalid/missing-colon.toon`](invalid/missing-colon.toon)** - Missing colon after key
  - Keys must be followed by `: ` (colon + space)
  - Demonstrates common syntax error
  - Spec: §6 Objects

- **[`invalid/path-expansion-conflict-strict.toon`](invalid/path-expansion-conflict-strict.toon)** - Path expansion conflict (v1.5+)
  - First line creates nested path `user.profile.name`, second line tries to assign primitive to `user.profile`
  - Fails when decoded with `expandPaths="safe"` and `strict=true` (default)
  - With `strict=false`, applies LWW conflict resolution (later value wins)
  - Spec: §13.4 Path Expansion, §14.5 Conflicts

- **[`invalid/key-folding-non-identifier.toon`](invalid/key-folding-non-identifier.toon)** - Non-identifier segments (v1.5+)
  - Contains keys like `first-name` with hyphens (not valid IdentifierSegments)
  - These remain as literal dotted keys when `expandPaths="safe"` is used
  - Demonstrates safe mode validation: only expands keys with valid identifier segments
  - Note: This is NOT an error—it's valid TOON, but shows when expansion doesn't occur
  - Spec: §13.4 Safe Mode Requirements, §1.9 IdentifierSegment

## Conversions

Side-by-side JSON ↔ TOON examples showing equivalent representations:

- **[`conversions/users.json`](conversions/users.json)** + **[`conversions/users.toon`](conversions/users.toon)**
  - Same tabular data in both formats
  - Shows token reduction achieved by TOON (≈30-60% for tabular data)
  - Demonstrates the primary use case: uniform arrays of objects

- **[`conversions/config.json`](conversions/config.json)** + **[`conversions/config.toon`](conversions/config.toon)** (v1.5+)
  - Deeply nested configuration data (server, database, logging settings)
  - TOON version uses dotted-key notation (`database.connection.host: localhost`)
  - Shows ≈40-50% token reduction for deeply nested structures
  - Demonstrates key folding benefits for configuration files

- **[`conversions/api-response.json`](conversions/api-response.json)** + **[`conversions/api-response.toon`](conversions/api-response.toon)** (v1.5+)
  - API response with nested data and metadata
  - TOON version collapses nested paths (`data.attributes.name: Ada Lovelace`)
  - Shows practical use case for serializing API responses
  - Demonstrates how key folding maintains readability while reducing tokens

## Using These Examples

These examples are useful for:

1. **Learning TOON syntax** - Study working examples to understand the format.
2. **Testing implementations** - Verify your parser/encoder handles these cases.
3. **Documentation** - Reference examples when explaining TOON features.
4. **Debugging** - Compare your output with these known-good examples.

For comprehensive test coverage, see the [`tests/`](../tests/) directory which contains language-agnostic JSON test fixtures covering all spec requirements.
