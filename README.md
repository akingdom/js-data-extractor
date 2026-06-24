# JS Data Extractor

A **pure‑Python** tool to extract JavaScript object and array literals from `.js` files – no regex, no `eval`, no external dependencies.

## Why?

- Your data is stored as JS variables (`const data = { ... }`) in auto‑generated files.
- You need to read that data into Python safely and reliably.
- JSON parsers fail on unquoted keys, trailing commas, comments, etc.
- Regular expressions corrupt strings and are hard to maintain.

This tool gives you a **production‑grade, hand‑written parser** that handles real‑world JavaScript data syntax.

## Features

- ✅ No regex – character‑by‑character scanning
- ✅ No `eval` – recursive‑descent parser, completely safe
- ✅ Single **and** double‑quoted strings with full escape sequences (`\n`, `\uXXXX`, etc.)
- ✅ Unquoted object keys (`{ foo: 1 }`)
- ✅ Trailing commas (`{ a: 1, }`)
- ✅ Comments – `//` and `/* ... */` are skipped
- ✅ Numeric literals: hex (`0x`), octal (`0o`), binary (`0b`), decimal, floats, exponents, `NaN`, `Infinity`, `-Infinity`
- ✅ `undefined` → `None`
- ✅ Extract **first** or **all** occurrences of one or more variables
- ✅ If no variable name is given, it finds the first object/array literal in the file
- ✅ Optional JSON output (`--json`, `--pretty`)
- ✅ Line/column error reporting for easy debugging
- ✅ Key memoization to reduce memory for large files

## Installation

Just download `jsextractor.py` and make it executable:

```bash
chmod +x jsextractor.py
```

No pip install needed – it uses only the Python standard library.

## Usage

### Command Line

```bash
# Extract the first assignment of 'thingCategories'
python jsextractor.py data.js thingCategories

# Extract all assignments of 'config' and 'settings'
python jsextractor.py data.js config settings --all

# Extract a single variable and print its value as JSON
python jsextractor.py data.js data --pretty

# Extract multiple variables as a JSON object
python jsextractor.py data.js user profile --json

# Let the tool find the first object/array literal (no variable name)
python jsextractor.py data.js --pretty
```

**Options:**

| Flag | Description |
|------|-------------|
| `--all` | Extract every occurrence of the variable(s) (default: first only). |
| `--first` | Extract only the first occurrence (default). |
| `--json` | Output the extracted data as compact JSON. |
| `--pretty` | Output as indented JSON (implies `--json`). |
| `--no-memoize` | Disable key memoization (use if memory is tight). |

### Programmatic Use

```python
from jsextractor import extract_js_variable

# Get the first assignment of 'thingCategories'
data = extract_js_variable('data.js', 'thingCategories')

# Get all assignments of 'config'
configs = extract_js_variable('data.js', 'config', occurrence='all')

# Disable key memoization (optional)
data = extract_js_variable('data.js', 'data', memoize_keys=False)
```

## How It Works

1. **Lexer** – scans the file character by character, skipping whitespace, comments, and strings.
2. **Parser** – a recursive‑descent parser that builds Python objects from the JS literal syntax.
3. **Extractor** – locates variable assignments of the form `[const|let|var] name = { ... }` or `name = [ ... ]`.
4. **Output** – returns native Python types (`dict`, `list`, `str`, `int`, `float`, `bool`, `None`).

## Limitations

The parser only handles **literals** – it does **not** evaluate:
- Function expressions, arrow functions, method definitions
- Computed property names (`[expr]: value`)
- Spread/rest operators
- Template literals with embedded expressions (they are skipped as strings)

These are not needed for static data extraction from generated JS files.

## License

MIT © 2026 – see the [LICENSE](LICENSE) file for details.
