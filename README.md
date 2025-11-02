# Taplo Configuration Templates

A collection of production-ready Taplo configuration templates for different project types. Each template provides sensible defaults optimized for git-friendly diffs, IDE integration, and language-specific conventions.

## Available Templates

### 1. **[minimal.taplo.toml](minimal.taplo.toml)** - Universal Default

**Use for**: Any project with TOML files, language-agnostic baseline

**Key features**:

- Git-friendly formatting (no alignment, trailing commas)
- 80-character line width
- Excludes common build directories
- Minimal, unopinionated configuration

**Best for**: Starting point for any project, configuration as code, multi-language projects without specific needs

---

### 2. **[rust.taplo.toml](rust.taplo.toml)** - Rust Projects

**Use for**: Rust projects with `Cargo.toml` files

**Key features**:

- Cargo.toml schema validation (JSON Schema from SchemaStore)
- Preserves Cargo.toml section order (never reorders keys)
- 100-character line width (matches rustfmt default)
- Excludes `target/` and `.cargo/` directories
- Special handling for `.cargo/config.toml`

**Best for**: Rust libraries, applications, and workspaces

---

### 3. **[python.taplo.toml](python.taplo.toml)** - Python Projects

**Use for**: Python projects with `pyproject.toml`

**Key features**:

- pyproject.toml schema validation (JSON Schema from SchemaStore)
- 88-character line width (matches Black formatter)
- Excludes `.venv/`, `__pycache__/`, `.pytest_cache/`, etc.
- Special handling for Poetry and PDM lock files
- Never reorders keys in lock files

**Best for**: Python packages, applications using Poetry/PDM/Hatch

---

### 4. **[javascript.taplo.toml](javascript.taplo.toml)** - JavaScript/TypeScript Projects

**Use for**: JS/TS projects using TOML for configuration

**Key features**:

- 100-character line width (common Prettier setting)
- Excludes `node_modules/`, `.next/`, `.nuxt/`, etc.
- Aligned with Prettier/Biome conventions
- Examples for Deno, Biome configs

**Best for**: Node.js, Deno, Bun projects with TOML configs, modern JS tooling

---

### 5. **[monorepo.taplo.toml](monorepo.taplo.toml)** - Monorepo Projects

**Use for**: Large monorepos with multiple languages/frameworks

**Key features**:

- Comprehensive exclusions for all major languages
- Rules for Rust, Python, JavaScript simultaneously
- Infrastructure/deployment config support (Docker, Railway, Fly, etc.)
- Workspace-aware with hierarchical configuration support
- 100-character line width as neutral default

**Best for**: Multi-language monorepos, polyglot projects, large organizations

---

## Quick Start

### Installation

1. **Choose a template** based on your project type
2. **Copy to your project root**:

   ```bash
   cp templates/rust.taplo.toml /path/to/your/project/.taplo.toml
   ```

   or

   ```bash
   cp templates/minimal.taplo.toml /path/to/your/project/taplo.toml
   ```

3. **Customize if needed** (see Customization Guide below)

4. **Test the configuration**:

   ```bash
   cd /path/to/your/project
   taplo format --check         # Dry run
   taplo lint                   # Validate
   ```

5. **Apply formatting**:

   ```bash
   taplo format
   ```

### File Naming

Taplo recognizes two filenames:

- `.taplo.toml` (hidden file, recommended)
- `taplo.toml` (visible file)

Both work identically. Use `.taplo.toml` to keep your project root clean.

---

## Configuration Philosophy

All templates follow these principles:

### 🎯 Git-Friendly

- `align_entries = false` by default (no alignment reduces diff noise)
- `array_trailing_comma = true` (cleaner diffs when adding items)
- `reorder_keys = false` (preserve author's intentional ordering)

### 📏 Consistent Line Width

- **80 chars**: Minimal template (conservative, universal)
- **88 chars**: Python template (matches Black)
- **100 chars**: Rust, JS, monorepo (modern standard)

### 🔒 Schema Validation

All templates include schema validation where available:

- `Cargo.toml` → `https://json.schemastore.org/cargo.json`
- `pyproject.toml` → `https://json.schemastore.org/pyproject.json`
- `taplo.toml` → `https://taplo.tamasfe.dev/configuration/config-schema.json`

This enables:

- IDE autocomplete
- Real-time validation
- Hover documentation

### 🚫 Comprehensive Exclusions

All templates exclude common build/dependency directories:

- Rust: `target/`, `.cargo/`
- Python: `.venv/`, `__pycache__/`, `.tox/`
- JavaScript: `node_modules/`, `.next/`, `dist/`

---

## Customization Guide

### Common Adjustments

#### Change line width

```toml
[formatting]
column_width = 120  # Your preferred width
```

#### Enable alignment for specific files

```toml
[[rule]]
include = ["config/**/*.toml"]
[rule.formatting]
align_entries = true  # Align = signs in config files
```

#### Reorder keys alphabetically

```toml
[[rule]]
include = ["settings.toml"]
[rule.formatting]
reorder_keys = true  # Alphabetical ordering
```

#### Add custom schema

```toml
[[rule]]
include = ["myproject.toml"]
schema = "https://example.com/schema.json"
```

#### Windows line endings

```toml
[formatting]
crlf = true  # Use CRLF instead of LF
```

### Rule Evaluation Order

Rules are evaluated from **most specific to least specific**:

```toml
# ❌ Wrong order - general rule first
[[rule]]
include = ["**/*.toml"]
# general formatting

[[rule]]
include = ["config/database.toml"]  # This won't apply!
# specific formatting

# ✅ Correct order - specific rule first
[[rule]]
include = ["config/database.toml"]
# specific formatting (wins)

[[rule]]
include = ["**/*.toml"]
# general formatting (fallback)
```

### Monorepo Hierarchical Configs

For fine-grained control in monorepos:

```
/
├── .taplo.toml              # Root config (workspace defaults)
├── apps/
│   └── api/
│       └── .taplo.toml      # API-specific overrides
└── packages/
    └── shared/
        └── .taplo.toml      # Package-specific overrides
```

Taplo merges configurations hierarchically, with child configs overriding parent settings.

---

## Testing & Validation

### Before Committing

1. **Check formatting** (dry run):

   ```bash
   taplo format --check
   ```

2. **Validate TOML syntax**:

   ```bash
   taplo lint
   ```

3. **Verbose mode** (troubleshooting):

   ```bash
   taplo format --check --verbose
   ```

4. **Format all files**:

   ```bash
   taplo format
   ```

### CI/CD Integration

Add to your CI pipeline:

```yaml
# GitHub Actions example
- name: Check TOML formatting
  run: |
    taplo format --check
    taplo lint
```

### IDE Integration

These configs work automatically with:

- **VS Code**: Even Taplo extension
- **Any LSP-compatible editor**: Taplo LSP reads `.taplo.toml`

No additional IDE configuration needed!

---

## When to Use Each Template

| Project Type | Recommended Template | Alternative |
|-------------|---------------------|-------------|
| Rust library/app | `rust.taplo.toml` | `minimal.taplo.toml` |
| Python package | `python.taplo.toml` | `minimal.taplo.toml` |
| Node.js/Deno | `javascript.taplo.toml` | `minimal.taplo.toml` |
| Polyglot monorepo | `monorepo.taplo.toml` | Language-specific in subdirs |
| Config-as-code | `minimal.taplo.toml` | - |
| Unknown/mixed | `minimal.taplo.toml` | Expand as needed |

---

## Common Patterns

### Pattern 1: Align Config Files Only

```toml
# Default: no alignment
[formatting]
align_entries = false

# Exception: config files
[[rule]]
include = ["config/**/*.toml", "settings/**/*.toml"]
[rule.formatting]
align_entries = true
```

### Pattern 2: Schema Validation for Custom Files

```toml
[[rule]]
include = ["src/config/app.toml"]
schema = "file://./schemas/app-config.schema.json"
```

### Pattern 3: Lock File Protection

```toml
[[rule]]
include = ["**/*.lock", "**/Cargo.lock"]
[rule.formatting]
reorder_keys = false      # Never reorder
reorder_arrays = false    # Never reorder
```

---

## Troubleshooting

### Configuration not applying?

1. Check file is named `.taplo.toml` or `taplo.toml`
2. Verify it's in your project root (or appropriate subdirectory)
3. Run `taplo format --check --verbose` to see rule matching

### Schema validation not working?

1. Ensure schema URL is accessible
2. Check for typos in schema URL
3. Verify TOML file structure matches schema

### Files not being formatted?

1. Check `include` patterns match your files
2. Verify files aren't in `exclude` patterns
3. Run with `--verbose` to see why files are skipped

### Rules conflicting?

1. Order rules from specific to general
2. Most specific match wins
3. Use `--verbose` to debug rule application

---

## Resources

- [Taplo Documentation](https://taplo.tamasfe.dev/)
- [Taplo CLI Reference](https://taplo.tamasfe.dev/cli/introduction.html)
- [JSON Schema Store](https://www.schemastore.org/) (for schema URLs)
- [TOML Specification](https://toml.io/)

---

## Contributing

To add a new template:

1. Follow the established structure
2. Include comprehensive comments
3. Provide schema validation where applicable
4. Test with real projects
5. Document use cases in this README

---

## License

These templates are provided as-is for use in any project. Modify freely to suit your needs.
