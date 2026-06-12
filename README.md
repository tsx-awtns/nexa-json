# nexa-json

JSON utilities for the Nexa programming language.

Write data in Nexa syntax — `nexa-json` converts it to JSON automatically.

---

## Install

```
nexa install nexa-json
```

## Import

```nxa
import Json from "nexa-json"
```

---

## Quick Start

```nxa
import Json from "nexa-json"

node User
    name   = "Mahmoud"
    level  = 15
    active = true

Json.write("data/user.json", User)

let loaded = Json.read("data/user.json")

show loaded.name    # Mahmoud
show loaded.level   # 15
show loaded.active  # true
```

The generated `data/user.json`:

```json
{
  "name": "Mahmoud",
  "level": 15,
  "active": true
}
```

---

## Native Nexa Object Syntax

Use Nexa-style `key = value` inside map and list literals — no JSON braces or colons required.

```nxa
let config = {
    name    = "my-app"
    version = "0.1.0"
    debug   = false
}

Json.write("config.json", config)
```

Nested objects:

```nxa
let profile = {
    username = "Arthur"
    stats    = {
        level = 15
        xp    = 2400
    }
}

Json.write("profile.json", profile)

let p = Json.read("profile.json")
show p.stats.xp          # 2400
```

List of objects:

```nxa
let users = [
    { name = "Arthur"  level = 10 },
    { name = "Noura"   level = 99 }
]

Json.write("users.json", users)

for u in users
    show "{u.name} — level {u.level}"
```

> Both Nexa-style `key = value` and JSON-style `"key": value` work inside map literals.
> Prefer `key = value` — it is the Nexa-native style.

---

## API Reference

### File operations

| Function | Description |
|---|---|
| `Json.read(path)` | Read and parse a JSON file. Returns the parsed value or `null`. |
| `Json.write(path, data)` | Write a node, map, list, or value to a JSON file. Auto-creates parent directories. |
| `Json.exists(path)` | Return `true` if the file exists. |
| `Json.delete(path)` | Delete the file. Will not delete directories. |
| `Json.create(path, data)` | Write a new file. Returns `false` (no overwrite) if file already exists. |
| `Json.clear(path)` | Reset a file to an empty object `{}`. |
| `Json.update(path, key, value)` | Read, set one key, write back. Returns updated object. |
| `Json.getIn(path, keyPath)` | Read file and get value at nested key path. |

### Parsing and serialization

| Function | Description |
|---|---|
| `Json.parse(text)` | Parse a JSON string. Returns `null` on invalid input. |
| `Json.stringify(data)` | Compact JSON string (no whitespace). |
| `Json.pretty(data, indent?)` | Indented JSON string. Default indent: 2. |

### In-memory data access

| Function | Description |
|---|---|
| `Json.get(data, key)` | Get a top-level value by key. |
| `Json.set(data, key, value)` | Set a top-level key. Returns updated object. |
| `Json.remove(data, key)` | Remove a key. Returns updated object. |
| `Json.has(data, key)` | Return `true` if the key exists. |
| `Json.keys(data)` | Return all top-level keys as a list. |
| `Json.values(data)` | Return all top-level values as a list. |
| `Json.merge(a, b)` | Merge two objects. Keys in `b` override `a`. |
| `Json.size(data)` | Number of top-level keys. |
| `Json.isEmpty(data)` | `true` if the object has no keys. |
| `Json.clone(data)` | Deep copy of the object. |
| `Json.toMap(value)` | Convert node/value to a plain JSON-safe map. |

### Nested path access

| Function | Description |
|---|---|
| `Json.getPath(data, path)` | Get value at nested path. `path` is a list of keys. |
| `Json.setPath(data, path, value)` | Set value at nested path. Creates intermediate objects. |
| `Json.deletePath(data, path)` | Delete value at nested path. |

---

## Node Serialization

Any Nexa node can be passed directly to `Json.write`. Public data properties are serialized automatically. Functions, actions, and runtime internals are skipped.

```nxa
node Player
    name   = "Arthur"
    health = 100
    level  = 5

Json.write("player.json", Player)
```

Result:

```json
{
  "name": "Arthur",
  "health": 100,
  "level": 5
}
```

---

## Nested Path Example

```nxa
let data = Json.read("config.json")

# Get deep value
let host = Json.getPath(data, ["server", "host"])

# Set deep value
let updated = Json.setPath(data, ["server", "port"], 8080)
Json.write("config.json", updated)

# Delete deep key
let cleaned = Json.deletePath(updated, ["server", "debug"])
```

---

## File Safety

- `Json.write` and `Json.create` automatically create all parent directories.
- `Json.delete` will not delete directories — only regular files.
- Paths are resolved relative to the working directory.
- Invalid JSON returns `null` with an error printed to stderr.

---

## Doctor Check

```bash
nexa doctor --pkg
```

The doctor checks that `nexa-json` is installed, importable, and that `Json.write` / `Json.read` / node serialization all work correctly.

---

## Examples

```bash
nexa run examples/basic.nxa          # Node serialization + file I/O
nexa run examples/native_object.nxa  # Nexa map/list literal syntax
```

---

## License

MIT
