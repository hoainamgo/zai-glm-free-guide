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

## 5️⃣ CẤU HÌNH HERMES AGENT

Thêm provider `OpenAI Compatible` trong config Hermes:
```ini
[providers.zai]
type = "openai"
base_url = "https://api.z.ai/api/paas/v4"
api_key = "YOUR_API_KEY"
model = "glm-4.7-flash"
```

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
