# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [1.5.0] - 2026-05-18

### Added

- **`reload_keys` tool** — re-read the `administrationId → apiKey` map from
  disk without restarting the MCP server. Sessions for keys that **changed**
  or were **removed** are evicted from the session cache automatically;
  sessions for unchanged keys stay warm. Returns a diff of `added`,
  `updated`, and `removed` IDs plus the resolved file path.
- **`loadApiKeysFile()` / `resolveApiKeysFilePath()` exports** in
  `yuki-client.ts` so the same file-resolution logic (env override →
  `~/.yuki/api-keys.json` → `./api-keys.json`) is used by both startup and
  the runtime reload tool.
- **`YukiClient.reloadApiKeys(next)` method** — mutates the in-memory key
  map in place (preserving the `Map` reference), invalidates only the
  sessions for changed/removed keys, and returns an `ApiKeyReloadDiff`.

### Changed

- `index.ts` now uses the shared `loadApiKeysFile()` helper at startup
  instead of reading and parsing the keys file inline. Pure refactor —
  behaviour and log output are unchanged.
- Tool count in the startup log line bumped from 30 → 31.

### Notes

Intended companion flow: after an external `create_api_key` operation (for
example from a sibling MCP server that drives the Yuki Integraties UI) has
written a new key to `~/.yuki/api-keys.json`, call `reload_keys` to make
that key immediately usable for SOAP tools — no MCP restart required.

## [1.4.0] - 2026-05-11

### Added

- `get_missing_invoices` tool to retrieve bank payments without a matching
  purchase invoice.

## [1.3.0] - 2026-03-31

### Added

- Multi-administration support via per-admin API key mapping.
- Keys-file loading at startup (`~/.yuki/api-keys.json` by default,
  overridable via `YUKI_API_KEYS_FILE`).
- Per-key session caching in `YukiClient` so that switching admins inside a
  single process does not re-authenticate against unchanged keys.

## [1.2.0] - 2026-03-27

### Added

- Tools: `get_gl_accounts_fiscal`, `get_net_revenue`, `list_documents`,
  `search_documents`, `get_document`, `download_document`,
  `get_cost_categories`, `get_administration_id`.

## [1.1.0] - 2026-03-23

### Added

- Backoffice tools: `get_workflow`, `get_outstanding_questions`.

## [1.0.0] - initial release

Initial public release of `@codemill-solutions/yuki-mcp`: an MCP server
wrapping the Yuki accounting SOAP API.
