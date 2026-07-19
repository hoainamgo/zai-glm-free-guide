---
name: hcnsec-api
description: >
  Hướng dẫn sử dụng API api.hcnsec.cn — aggregator đa model (DeepSeek-V4-Flash, GLM, Kimi, MiniMax, Qwen, Step, v.v).
  Base URL OpenAI-compatible, 1 key truy cập nhiều model. Đã test thực tế ✅.
  Lưu ý: nhiều model có thinking mode mặc định BẬT → content rỗng nếu không tắt.
triggers:
  - "hcnsec"
  - "api.hcnsec.cn"
  - "deepseek v4 flash"
  - "step-3.7-flash"
  - "aggregator model"
metadata:
  base_url: "https://api.hcnsec.cn/v1"
  auth: "Bearer token (1 key, nhiều model)"
  pricing: "Free/cheap tier (kiểm tra thực tế)"
  models_tested: 19
---

# api.hcnsec.cn — Aggregator API Hướng dẫn

> ✅ OpenAI-compatible (`/v1/chat/completions`). 1 key dùng được ~19 model.
> ⚠️ Nhiều model BẬT thinking mode mặc định → content rỗng nếu không thêm `thinking: disabled`.

---

## 1️⃣ THÔNG TIN CƠ BẢN

| Thông số | Giá trị |
|---|---|
| Base URL | `https://api.hcnsec.cn/v1` |
| Endpoint | `/chat/completions` |
| Auth | `Authorization: Bearer <KEY>` |
| Key env | `HCNSEC_API_KEY` |

---

## 2️⃣ MODEL CÓ SẴN (đã test thực tế 2026-07-19)

### ✅ Chạy tốt (15/19)
- `auto` → route tự động (test trả `step-3.7-flash`)
- `DeepSeek-V4-Flash` ⚠️ (thinking on → content rỗng, tắt là có)
- `DeepSeek-V4-Pro`
- `glm-5.2` ⚠️ (thinking on → empty)
- `Kimi-K2.6`
- `MiniMax-M2.7`, `MiniMax-M3`
- `Qwen3.5-397B-A17B`, `Qwen3.6-35B-A3B`
- `step-3.5-flash`, `step-3.7-flash`
- `kat-coder-pro-v2`, `kat-coder-pro-v2.5`
- `sensenova-6.7-flash-lite`, `sensenova-u1-fast` ⚠️ (empty do thinking)
- `step-router-v1`

### ❌ Không chạy (3/19)
- `glm-4.7` → invalid (thử `glm-4.7-flash`?)
- `glm-5.1` → model_not_found
- `Spark-X2-Flash` → invalid

---

## 3️⃣ GỌI API (template)

### cURL
```bash
curl -s "https://api.hcnsec.cn/v1/chat/completions" \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer $HCNSEC_API_KEY" \
  -d '{
    "model": "DeepSeek-V4-Flash",
    "messages": [{"role":"user","content":"Xin chào"}],
    "thinking": {"type": "disabled"}
  }'
```

### Python (OpenAI SDK)
```python
from openai import OpenAI
client = OpenAI(api_key=HCNSEC_API_KEY, base_url="https://api.hcnsec.cn/v1")
resp = client.chat.completions.create(
    model="DeepSeek-V4-Flash",
    messages=[{"role":"user","content":"Hello"}],
    extra_body={"thinking": {"type": "disabled"}}  # ⚠️ BẮT BUỘC cho model có thinking
)
print(resp.choices[0].message.content)
```

---

## 4️⃣ ⚠️ LƯU Ý THINKING MODE (quan trọng)

Nhiều model trên hcnsec (DeepSeek-V4-Flash, glm-5.2, sensenova-*) mặc định BẬT thinking.
→ Trả `content` rỗng, token đổ vào `reasoning_content`.

**Fix:** luôn thêm `"thinking": {"type": "disabled"}` vào request body.

Khi cấu hình Hermes: dùng `extra_body` trong provider config (Hermes forward verbatim).

---

## 5️⃣ CẤU HÌNH HERMES (provider hcnsec)

```yaml
# config.yaml (profile Lysa)
providers:
  hcnsec:
    base_url: https://api.hcnsec.cn/v1
    key_env: HCNSEC_API_KEY
    extra_body:
      thinking:
        type: disabled
```

Switch model: `/model` → chọn `hcnsec/DeepSeek-V4-Flash` (hoặc model khác đã test).

**Pitfall giống zai-glm-free:**
1. Phải thêm `hcnsec` vào `auth.json` (provider authenticated) thì picker mới hiện.
2. `models.py` `_PROVIDER_MODELS["hcnsec"]` cần có model name (patch core, re-apply sau `hermes update`).
3. `config.yaml default` có thể bị revert → verify trên disk trước khi restart.
4. Restart gateway profile để áp dụng.

---

## 6️⃣ KIỂM TRA KEY CÒN HẠN
```bash
curl -s -o /dev/null -w "%{http_code}" "https://api.hcnsec.cn/v1/models" \
  -H "Authorization: Bearer $HCNSEC_API_KEY"
```
(Note: endpoint /models có thể không có trên aggregator này — test bằng cách gọi 1 model cụ thể)
