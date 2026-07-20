---
name: hhtech-anthropic
description: >
  Add HHtech API (hhtechapi.net) as Anthropic-compatible provider for Lysa / Hermes agents.
  Endpoint https://hhtechapi.net/v1/messages (native Anthropic format, x-api-key header).
  Models: claude-haiku-4.5, claude-opus-4-8, claude-sonnet-5, claude-fable-5.
  Use when user wants to add this aggregator or debug anthropic-type provider in Hermes.
---

# HHtech API — Anthropic-compatible Provider

## ENDPOINT
```
POST https://hhtechapi.net/v1/messages
Headers:
  x-api-key: <KEY>
  anthropic-version: 2023-06-01
  content-type: application/json
Body: {"model":"claude-haiku-4.5","max_tokens":128,"messages":[{"role":"user","content":"Xin chào"}]}
```

## MODELS AVAILABLE
- `claude-haiku-4.5` ✅ tested
- `claude-opus-4-8` ✅ tested
- `claude-sonnet-5` (assumed)
- `claude-fable-5` (assumed)

## HERMES CONFIG (config.yaml)
```yaml
providers:
  hhtech:
    type: anthropic
    base_url: https://hhtechapi.net
    key_env: HHTECH_API_KEY
    extra_headers:
      anthropic-version: "2023-06-01"
```

## .ENV (per-profile, chmod 600)
```
HHTECH_API_KEY=sk-xxxxxxxx
```

## AUTH.JSON (Hermes core)
```json
{
  "providers": {
    "hhtech": {"authenticated": true, "api_key_env": "HHTECH_API_KEY", "base_url": "https://hhtechapi.net"}
  }
}
```

## MODELS.PY (hermes_cli/models.py — _PROVIDER_MODELS)
```python
"hhtech": [
    "claude-haiku-4.5",
    "claude-opus-4-8",
    "claude-sonnet-5",
    "claude-fable-5",
],
```
⚠️ Patch models.py KHÔNG backup bởi cron → chạy `reapply_patches.py` (skill hcnsec-api) sau `hermes update`, hoặc thêm vào reapply script.

## PITFALLS
1. **type MUST be `anthropic`** (not openai-compatible) — endpoint is `/v1/messages` native Anthropic.
2. **extra_headers anthropic-version required** — HHtech checks it.
3. **Key format `sk-...`** — save to `.env`, never plaintext in scripts/cron.
4. **models.py patch lost on hermes update** — re-apply manually or via reapply_patches.py.
5. **Restart gateway** sau đổi config để Hermes nhận provider mới.

## TEST
```bash
curl -s https://hhtechapi.net/v1/messages \
  -H "x-api-key: $HHTECH_API_KEY" \
  -H "anthropic-version: 2023-06-01" \
  -H "content-type: application/json" \
  -d '{"model":"claude-haiku-4.5","max_tokens":64,"messages":[{"role":"user","content":"Say hi"}]}'
```
