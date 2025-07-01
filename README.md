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

## Example INI

```ini
[server]
host: 127.0.0.1
port: "8080"  # quoted string
message: "Welcome! #1"
```

## Usage

```haskell
import qualified Data.Text.IO as TIO
import ConfigParser
import qualified Data.Map.Strict as Map

main :: IO ()
main = do
  raw <- TIO.readFile "config.ini"
  case parseConfig raw of
    Left err -> putStrLn $ "Parse error:\n" ++ err
    Right cfg -> do
      let section = cfg Map.! "server"
      print section
      -- Required field
      case getField "server" "host" section of
        Left err -> putStrLn err
        Right host -> putStrLn $ "Host: " ++ show host
      -- Optional field
      let port = getOptionalField "server" "port" "80" section
      putStrLn $ "Port: " ++ show port

      -- Type conversion helpers
      case getFieldInt "server" "port" section of
        Left err -> putStrLn err
        Right portNum -> putStrLn $ "Port as Int: " ++ show portNum
      case getFieldBool "server" "enabled" section of
        Left err -> putStrLn err
        Right enabled -> putStrLn $ "Enabled: " ++ show enabled
```

## API

- **parseConfig :: Text -> Either String Config**  
  Parse a config file from `Text`. Returns either an error message or the parsed config.

- **getField :: Text -> Text -> ConfigSection -> Either String Text**  
  Get a required field from a section. Returns an error if the key is missing.

- **getOptionalField :: Text -> Text -> Text -> ConfigSection -> Text**  
  Get an optional field from a section, with a default value.

- **writeConfig :: Config -> Text**  
  Serialize the config back to INI format.

- **getFieldAs :: IniReadable a => Text -> Text -> Text -> ConfigSection -> Either String a**  
  Get a required field and convert it to any supported type. Returns an error if missing or conversion fails.

- **getFieldInt :: Text -> Text -> ConfigSection -> Either String Int**  
  Get a required field and convert to Int.

- **getFieldDouble :: Text -> Text -> ConfigSection -> Either String Double**  
  Get a required field and convert to Double.

- **getFieldBool :: Text -> Text -> ConfigSection -> Either String Bool**  
  Get a required field and convert to Bool (accepts true/false/yes/no/1/0).

- **getFieldText :: Text -> Text -> ConfigSection -> Either String Text**  
  Get a required field as Text.

## Strictness

- Duplicate sections or keys are errors.
- Missing required keys are errors.

## Quoted Values

- Use quotes to include `#`, `:`, or spaces in values:
  ```ini
  host: "127.0.0.1 #comment"
  ```

## Error Handling

- If a required key is missing, or a section/key is duplicated, `parseConfig` will return a descriptive error message.
- Example error:
  ```
  Duplicate section: [server]
  ```
