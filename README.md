# strict-ini

**strict-ini** is a safe, minimal INI-style configuration parser written in Haskell. It supports:

- Sections with `[name]`
- `key: value` syntax
- Full-line and inline comments
- Quoted strings for values like `#`, `:` etc.
- Strict erroring on:
  - Duplicate sections
  - Duplicate keys
  - Missing required keys

## Example

```ini
[server]
host: 127.0.0.1
port: "8080"  # quoted string
message: "Welcome! #1"
