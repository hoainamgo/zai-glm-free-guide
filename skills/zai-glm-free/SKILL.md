---
name: zai-glm-free
description: >
  Hướng dẫn chi tiết sử dụng Z.AI GLM-4.7-Flash (free 100%) — đăng ký, lấy API key, gọi API (Python/cURL), lưu ý thinking mode, giới hạn free tier.
  Bao gồm template Python thuần (requests), OpenAI SDK, cURL, và cấu hình Hermes.
  Đã test thực tế ✅ — thay thế HY3 (hết 21/7) và các model free khác.
triggers:
  - "z.ai"
  - "zai"
  - "glm-4.7-flash"
  - "glm free"
  - "zhipu"
  - "đăng ký z.ai"
  - "lấy api z.ai"
  - "hướng dẫn z.ai"
  - "free llm api"
  - "thinking mode"
  # Class-level entry points — this skill also documents how to wire ANY
  # custom OpenAI-compatible provider into a Hermes profile + debug the picker.
  - "model không chọn được"
  - "model không hiện trong picker"
  - "model not found in picker"
  - "add custom provider hermes"
  - "custom openai-compatible provider"
  - "provider trong auth.json"
  - "content trả về rỗng"
  - "reasoning_content rỗng"
  - "hcnsec"
  - "api.hcnsec.cn"
  - "thêm provider mới hermes"
  - "ProviderConfig auth.py"
  - "aggregator openai-compatible"
metadata:
  free: true
  context: 200K
  output: 128K
  pricing: "Free 100% (input + output + cache)"
  auth: "Email/Google — không cần số TQ"
  base_url: "https://api.z.ai/api/paas/v4"
  models:
    - glm-4.7-flash
    - glm-4.5-flash
---

# Z.AI GLM-4.7-Flash — Hướng dẫn sử dụng FREE 100%

> ✅ **Thay thế HY3 (hết 21/7)** — 200K context, 30B MoE, coding ngon, **hoàn toàn free** (input + output + cache).
> ✅ **Đăng ký bằng email/Google — không cần số điện thoại TQ** (đã verify qua tài liệu chính thức + test thực tế).

---

## 1️⃣ ĐĂNG KÝ & LẤY API KEY

### Bước 1 — Đăng ký tài khoản
1. Vào [https://z.ai/chat](https://z.ai/chat)
2. Bấm **Login** (góc trên bên phải) → chọn **Sign up**
3. Đăng ký bằng **email thường** hoặc **Google account** (không cần số điện thoại)
4. Mở hộp thư → nhấn link xác nhận Z.AI gửi về → tài khoản active

### Bước 2 — Tạo API Key
1. Sau khi login, vào **menu profile** (góc phải) → chọn **API Keys**
2. Bấm **Create a new API key** → đặt tên (vd: `hermes-agent`)
3. **Copy key ngay** (chỉ hiện 1 lần!)

> 🔗 Nhanh hơn: truy cập thẳng [https://z.ai/manage-apikey/apikey-list](https://z.ai/manage-apikey/apikey-list) sau khi login.

---

## 2️⃣ THÔNG TIN CƠ BẢN

| Thông số | Giá trị |
|---|---|
| Base URL | `https://api.z.ai/api/paas/v4` |
| Model free | `glm-4.7-flash` hoặc `glm-4.5-flash` |
| Context | 200K tokens |
| Max output | 128K tokens |
| Giá | **Free 100%** (input + output + cache) |
| Auth | Email/Google — **không cần số TQ** |

---

## 3️⃣ 3 CÁCH GỌI API (template copy-paste)

### 🅰️ cURL (test nhanh)
```bash
curl -s "https://api.z.ai/api/paas/v4/chat/completions" \
  -H "Authorization: Bearer YOUR_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "model": "glm-4.7-flash",
    "messages": [{"role":"user","content":"Chào bạn"}],
    "max_tokens": 200,
    "thinking": {"type": "disabled"}
  }'
```

### 🅱️ Python thuần (chỉ cần `requests`)
```python
import requests

url = "https://api.z.ai/api/paas/v4/chat/completions"
headers = {
    "Authorization": "Bearer YOUR_API_KEY",
    "Content-Type": "application/json",
}
payload = {
    "model": "glm-4.7-flash",
    "messages": [{"role": "user", "content": "Giải thích ngắn gọn về AI"}],
    "max_tokens": 300,
    "thinking": {"type": "disabled"},
}

resp = requests.post(url, headers=headers, json=payload)
print(resp.json()["choices"][0]["message"]["content"])
```

### 🅲️ Python (OpenAI SDK)
```python
from openai import OpenAI

client = OpenAI(
    api_key="YOUR_API_KEY",
    base_url="https://api.z.ai/api/paas/v4/",
)

resp = client.chat.completions.create(
    model="glm-4.7-flash",
    messages=[{"role":"user","content":"Viết 1 đoạn văn ngắn về khủng long"}],
    max_tokens=300,
    extra_body={"thinking": {"type": "disabled"}},  # ⚠️ Tắt thinking mode
)
print(resp.choices[0].message.content)
```

---

## 4️⃣ ⚠️ LƯU Ý QUAN TRỌNG: THINKING MODE

> **GLM-4.7-Flash mặc định BẬT thinking mode** → nó dùng token để "suy nghĩ" (`reasoning_content`), trả về `content` **rỗng** nếu `max_tokens` nhỏ.

| Trường hợp | Cách xử lý |
|---|---|
| Muốn trả lời **nhanh, gọn** (chat thường, agent task) | `"thinking": {"type": "disabled"}` |
| Muốn **reasoning sâu** (coding phức tạp, toán) | `"thinking": {"type": "enabled"}` + `max_tokens` lớn (≥2000) |

👉 **Với agent (làm PM, chat, viết content): khuyên để `disabled`** cho nhanh và tiết kiệm.

---

## 5️⃣ CẤU HÌNH HERMES AGENT (đã test thực tế trên Lysa/agent2 ✅)

Config Hermes dùng **YAML** (`config.yaml`), KHÔNG phải `.ini`. Key đặt trong `.env`, config trỏ qua `key_env`:

```yaml
model:
  default: glm-4.7-flash
  provider: zai
providers:
  zai:
    base_url: https://api.z.ai/api/paas/v4
    key_env: ZAI_API_KEY          # tên biến, KHÔNG dán key vào config
    extra_body:                    # ⚠️ BẮT BUỘC — nếu thiếu, content trả về RỖNG
      thinking:
        type: disabled
```

Thêm key vào `.env` (perms 600): `echo 'ZAI_API_KEY=...' >> ~/.hermes/.env` (hoặc profile `.env` tương ứng, vd `~/.hermes/profiles/agent2/.env` cho Lysa).

### ⚠️ 3 pitfall khi cắm vào Hermes (mất nhiều round-trip nếu không biết)

1. **`extra_body.thinking.type: disabled` là BẮT BUỘC.** Hermes không tự thêm flag thinking. Không có nó → GLM-4.7-Flash trả `content=''` (rỗng), token đổ hết vào `reasoning_content`. Provider config Hermes hỗ trợ `extra_body` — forward verbatim vào mọi request. Đây là fix đúng, không cần wrapper script.

2. **Tên model free là `glm-4.7-flash`, KHÔNG phải `glm-4.7`.** `glm-4.7` là bản trả phí ($0.6/$0.11 per 1M). Dễ nhầm khi Hermes gợi ý "Similar models: glm-4.7, glm-4.5-air, glm-4.6".

3. **Z.ai `/models` listing KHÔNG trả `glm-4.7-flash`** (chỉ trả glm-4.5/4.6/4.7/5.x). Nên UI picker `/model` của Hermes KHÔNG hiện `glm-4.7-flash` — NHƯNG model vẫn gọi được bình thường khi set thẳng trong `config.yaml default:`. Đừng hoang mang khi "không thấy model trong list"; test bằng curl thực tế thay vì tin listing. Xóa cache (`rm provider_models_cache.json models_dev_cache.json context_length_cache.yaml` trong profile dir) không giúp hiện — vì listing gốc từ Z.ai đã thiếu.
   **FIX để hiện trong picker:** Hermes có curated list `_PROVIDER_MODELS["zai"]` trong `~/.hermes/hermes-agent/hermes_cli/models.py` (~line 275). Thêm dòng `"glm-4.7-flash",` vào list đó → picker sẽ hiện. ⚠️ Đây là patch CORE Hermes → `hermes update` sẽ ghi đè, phải re-apply sau mỗi lần update.

4. **⭐ ROOT CAUSE khi "Model không chọn được" trong picker: provider `zai` PHẢI có trong `auth.json` của profile.** Hermes chỉ liệt kê model của provider đã "authenticated" (có entry trong auth_store/auth.json). Config.yaml + models.py đúng mà picker vẫn trống nghĩa là thiếu bước này. Kiểm tra: `python3 -c "import json; print(list(json.load(open('auth.json'))['providers'].keys()))"` chạy trong profile dir — nếu chỉ có `nous` mà không có `zai` → đó là nguyên nhân. FIX (thêm zai vào auth.json, key lấy từ .env):
   ```python
   import json
   p='auth.json'; d=json.load(open(p))
   d.setdefault('providers',{})['zai']={'authenticated':True,'api_key':KEY,'base_url':'https://api.z.ai/api/paas/v4','provider_id':'zai'}
   json.dump(d,open(p,'w'),indent=2)
   ```
   Hermes nhận key qua env var names `GLM_API_KEY` / `ZAI_API_KEY` / `Z_AI_API_KEY` (xem auth.py PROVIDER_REGISTRY). Bản chính thức: `hermes --profile <name> auth login zai` cũng ghi vào auth.json — dùng nếu có sẵn CLI flow.

6. **⭐ PROVIDER HOÀN TOÀN MỚI (chưa có trong Hermes core) cần THÊM `ProviderConfig` vào `auth.py`.** `zai` đã ship sẵn trong Hermes nên chỉ cần 4 bước trên. Nhưng khi thêm provider gateway MỚI chưa từng có (vd `hcnsec` = api.hcnsec.cn), Hermes không biết provider đó → phải thêm entry vào `PROVIDER_REGISTRY` trong `~/.hermes/hermes-agent/hermes_cli/auth.py` (~line 243, ngay cạnh block `zai`):
   ```python
   "hcnsec": ProviderConfig(
       id="hcnsec",
       name="HCNSEC API",
       auth_type="api_key",
       inference_base_url="https://api.hcnsec.cn/v1",
       api_key_env_vars=("HCNSEC_API_KEY",),
       base_url_env_var="HCNSEC_BASE_URL",
   ),
   ```
   → Đây là patch CORE thứ 2 (cùng `models.py`), `hermes update` sẽ ghi đè cả 2, phải re-apply. Cũng thêm `ProviderEntry("hcnsec", "HCNSEC API", "...")` vào list ProviderEntry (~line 1052 models.py) để hiện tên đẹp trong picker.

**CHECKLIST đầy đủ khi thêm 1 provider OpenAI-compatible MỚI vào profile (thứ tự):**
1. `.env`: thêm `<PROVIDER>_API_KEY=...` (chmod 600)
2. `config.yaml`: block `providers.<name>` (base_url + key_env + `extra_body.thinking.disabled` nếu model có thinking)
3. `auth.json`: `providers.<name> = {authenticated:True, api_key, base_url, provider_id}`
4. `models.py`: `_PROVIDER_MODELS["<name>"] = [...]` (danh sách model đã test) + `ProviderEntry(...)`
5. `auth.py`: `ProviderConfig(...)` trong PROVIDER_REGISTRY — **CHỈ cần nếu provider chưa có sẵn trong Hermes core**
6. `ast.parse` verify models.py + auth.py không lỗi syntax
7. Restart gateway profile (user tự chạy nếu bị cấm)

**Thứ tự debug "model không hiện/không chọn được" (đi từ rẻ→đắt):** (a) config.yaml có `default` + `provider` + `extra_body.thinking.disabled`? → (b) tên model đúng bản free `glm-4.7-flash`? → (c) `auth.json` có entry provider chưa? (root cause hay gặp nhất) → (d) curated `_PROVIDER_MODELS` trong models.py có model chưa? → (e) provider có trong `auth.py` PROVIDER_REGISTRY chưa? (chỉ với provider mới hoàn toàn) → (f) đã restart gateway profile chưa?

5. **Config.yaml `default` có thể bị REVERT giữa các lần sửa.** Trong 1 phiên, `default: glm-4.7-flash` đã bị ghi đè ngược về `glm-4.7` (patch tool cảnh báo "modified since you last read / external edit"). FIX: sau MỖI lần sửa `default`, **đọc lại ngay** (`grep 'default:' config.yaml`) xác nhận không bị revert; nếu revert, sửa lại bằng python read→replace→write rồi verify lần nữa TRƯỚC khi restart gateway. Đừng tin patch báo thành công là xong — verify trên disk.

Sau khi patch config → cần **restart gateway** của profile để áp dụng (`hermes --profile <name> gateway restart`). Lưu ý: một số môi trường cấm agent tự restart gateway của profile khác — khi đó đưa lệnh cho user tự chạy.

---

## 6️⃣ GIỚI HẠN FREE TIER (thực tế test)
- Z.AI **không công bố RPD/RPM cụ thể** → test thực tế: **5–10 request song song ổn định**
- Cao điểm (2–6PM giờ Singapore) có thể bị throttle
- Web Search tool **tính phí $0.01/lần** (dù model free)
- Free tier "generous until it isn't" → không nên dùng cho production critical

---

## 7️⃣ BẢNG MODEL Z.AI (chọn theo nhu cầu)

| Model | Free? | Dùng khi |
|---|---|---|
| `glm-4.7-flash` | ✅ Free | Mặc định — nhanh, coding ngon, đủ mọi task |
| `glm-4.5-flash` | ✅ Free | Dự phòng nếu flash bị limit |
| `glm-4.7` | ❌ $0.6/$0.11 per 1M | Cần reasoning mạnh hơn (như Sonnet) |
| `glm-5.2` | ⏳ Limited-time free | Flagship mới nhất, context 1M |

---

## 8️⃣ VÍ DỤ THỰC TẾ (đã test ✅)

### 📌 Viết nội dung cho Dino Universe
```python
prompt = """
Viết 1 đoạn văn ngắn (5-6 câu) giới thiệu về Dino Universe — một thế giới khủng long thông minh,
biết sử dụng công nghệ và xây dựng nền văn minh riêng. Đoạn văn cần hấp dẫn, phù hợp cho trẻ em
và người lớn, gợi mở trí tưởng tượng về một loài khủng long tiến hóa vượt bậc.
"""

# Kết quả mẫu:
# "Dino Universe là một hành tinh bí ẩn nơi những con khủng long không chỉ sống sót mà còn vươn lên
# làm chủ công nghệ hiện đại. Họ chế tạo máy móc sắc bén, xây dựng thành phố vĩ đại và phát triển
# hệ thống năng lượng độc đáo. Chúng ta sẽ được chứng kiến những cuộc đua tốc độ giữa khủng long
# hay những bảo tàng kỹ thuật số hoành tráng. Thế giới này là minh chứng cho sự tiến hóa phi thường,
# biến loài bò sát cổ đại thành những người bạn thông minh, tận tâm."
```

---

## 9️⃣ FAQ

### ❓ Tại sao `content` trả về rỗng?
→ Vì **thinking mode mặc định BẬT**. Tắt nó đi: `"thinking": {"type": "disabled"}`.

### ❓ Làm sao biết key còn hoạt động?
```bash
curl -s -o /dev/null -w "HTTP %{http_code}" "https://api.z.ai/api/paas/v4/models" \
  -H "Authorization: Bearer YOUR_API_KEY"
```
→ HTTP 200 = key hợp lệ.

### ❓ Phân biệt lỗi 429 (đọc kỹ `code`, đừng nhầm)
- `code 1113` = **"Insufficient balance or no resource package"** (余额不足) → tài khoản HẾT gói tài nguyên, phải nạp/kích hoạt gói free. Model free vẫn cần account có resource package.
- `code 1302` = **"Rate limit reached"** → chỉ throttle tạm thời (gọi nhiều/cao điểm), đợi vài giây rồi retry là được. KHÔNG phải hết tiền.
- Đổi được từ `not found` → `1302` khi sửa tên model đúng nghĩa là model đã tồn tại, chỉ đang bị rate-limit — retry.

### ❓ Có cần số điện thoại TQ không?
→ **Không**. Đăng ký bằng email/Google là đủ (đã verify qua tài liệu + test thực tế).

### ❓ Web Search có free không?
→ **Không**. Dù model free nhưng web search tool tốn **$0.01/lần**.

---

## 📌 TÓM TẮT
- ✅ **Free 100%** (input + output + cache) — xác nhận từ docs.z.ai pricing
- ✅ **Không cần số TQ** — đăng ký email/Google
- ✅ 200K context, 30B MoE, coding ngon — thay thế HY3 (hết 21/7)
- ⚠️ **Thinking mode mặc định BẬT** → tắt đi nếu muốn trả lời nhanh
- ⚠️ Web Search tool **tính phí $0.01/lần**

---

## 🔗 TÀI LIỆU THAM KHẢO
- [Z.AI Pricing Docs](https://docs.z.ai/guides/overview/pricing)
- [Z.AI API Reference](https://docs.z.ai/api-reference/introduction)
- [Z.AI Quick Start](https://docs.z.ai/guides/overview/quick-start)
- [OpenRouter GLM-4.7-Flash](https://openrouter.ai/models/z-ai/glm-4.7-flash)
- [GitHub ZtoApi Registration Workflow](https://github.com/hulisang/ZtoApi/blob/main/deno/zai/zai_register.md)

---

## 📎 SUPPORT FILES
- `references/probing-aggregator-apis.md` — how to discover the real model names
  on new-api/one-api style aggregator gateways (DeepSeek/GLM/Step/Kimi/MiniMax
  distributors like hcnsec.cn/iamhc.cn): use `model:"auto"` + `/v1/models`, don't
  guess names, watch the exact host.
