# 2ToDo Parser Config

This directory contains the parser configuration files that can be updated remotely without requiring an app update.

## How It Works

1. The app checks GitHub for updated config files on launch
2. If a new version is found, it downloads and caches the config
3. If GitHub is unavailable, it uses the cached or bundled config

## Config Files

Each parser type has its own separate config file:

| File | Description |
|------|-------------|
| `cruise-parser-config.json` | Cruise reservation parsing patterns |
| `hotel-parser-config.json` | Hotel reservation parsing patterns |
| `flight-parser-config.json` | Flight reservation parsing patterns |
| `car-rental-parser-config.json` | Car rental parsing patterns |
| `train-parser-config.json` | Train reservation parsing patterns |
| `smart-list-parser-config.json` | Smart list / natural language patterns |

## Updating Patterns

To update parsing patterns:

1. Edit the relevant JSON config file
2. **Increment the `version` field** (e.g., "1.0.0" → "1.0.1")
3. Commit and push to the `main` branch
4. The app will detect the new version on next launch

## Config Structure

### Cruise Parser Config Example

```json
{
    "version": "1.0.0",
    "lastUpdated": "2026-01-17",
    "knownCruiseLines": {
        "royal caribbean": "Royal Caribbean",
        "carnival": "Carnival"
    },
    "shipNamePatterns": [
        "(?i)ship\\s*name:?\\s+([A-Za-z][A-Za-z\\s]+)"
    ],
    "shipNameFalsePositives": [
        "cruise length", "cruise line"
    ],
    "guestFalsePositives": [
        "PAYMENT", "TOTAL", "BALANCE"
    ],
    "portFalsePositives": [
        "itinerary", "arrive", "depart"
    ]
}
```

### Key Fields

- **version**: Semantic version string (required for update detection)
- **patterns**: Array of regex patterns (escape backslashes as `\\`)
- **falsePositives**: Strings to filter out from results
- **known<Type>**: Dictionary mapping lowercase keywords to display names

## Regex Patterns

Patterns use standard regex syntax with these considerations:

1. **Escape backslashes** in JSON: `\s` becomes `\\s`
2. **Case insensitive**: Use `(?i)` at the start
3. **Multiline mode**: Use `(?m)` for multiline matching
4. **Capture groups**: Use `()` to capture the matched value

### Common Patterns

```
(?i)           - Case insensitive flag
(?m)           - Multiline flag
\\s            - Whitespace
\\d            - Digit
[A-Za-z]       - Letter
[^\\n]+        - Any characters except newline
(?:\\n|$)      - Newline or end of string
```

## Remote URL

The app fetches configs from:
```
https://raw.githubusercontent.com/AntiquatedCoding/2ToDo-Parser-Config/main/<filename>
```

To use a different repository, update `baseRemoteURL` in `ParserConfigurationService.swift`.

## Fallback Behavior

1. **First launch**: Uses bundled config
2. **With internet**: Downloads and caches latest config
3. **No internet**: Uses cached config (if available) or bundled config
4. **Error**: Falls back to cached or bundled config

## Testing Changes

1. Update the JSON file locally in `Resources/ParserConfigs/`
2. Increment the version
3. The bundled config in `ParserConfigurationService.bundledXxxConfig()` serves as the fallback
4. Test parsing with sample PDFs
5. Once verified, push to GitHub
