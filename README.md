# Z.AI GLM-4.7-Flash + HCNSEC API — Free Model Guides & Hermes Skills

Bộ hướng dẫn + skill Hermes để cài **model free** cho agent (đã test thực tế trên Lysa/agent2).

## 📦 Skills (dành cho agent khác học)

| Skill | Mô tả |
|---|---|
| [`skills/zai-glm-free`](skills/zai-glm-free/SKILL.md) | Z.AI GLM-4.7-Flash (free 100%) — đăng ký, gọi API, thinking mode, cấu hình Hermes |
| [`skills/hcnsec-api`](skills/hcnsec-api/SKILL.md) | api.hcnsec.cn aggregator — 1 key ~19 model (DeepSeek-V4-Flash, GLM, Kimi, MiniMax, Qwen, Step) |

## 🚀 Cài đặt nhanh cho Hermes agent

```bash
# 1. Copy skill vào Hermes
cp -r skills/zai-glm-free ~/.hermes/skills/mlops/
cp -r skills/hcnsec-api ~/.hermes/skills/mlops/

# 2. Thêm key vào .env (profile tương ứng)
echo 'ZAI_API_KEY=your_key' >> ~/.hermes/profiles/agent2/.env
echo 'HCNSEC_API_KEY=your_key' >> ~/.hermes/profiles/agent2/.env

# 3. Patch models.py + auth.py (Hermes core) — xem chi tiết trong SKILL.md
# Thêm 'glm-4.7-flash' vào _PROVIDER_MODELS['zai']
# Thêm 'hcnsec' ProviderConfig + _PROVIDER_MODELS['hcnsec']

# 4. Thêm provider vào config.yaml + auth.json của profile
# 5. Restart gateway
```

## ⚠️ Pitfalls quan trọng (đã gặp thực tế)

1. **Thinking mode mặc định BẬT** → content rỗng. Fix: `extra_body: {thinking: {type: disabled}}` trong provider config.
2. **Tên model free = `glm-4.7-flash`** (KHÔNG phải `glm-4.7` — bản trả phí).
3. **`/models` listing không có `glm-4.7-flash`** → phải patch `models.py` curated list.
4. **Provider phải có trong `auth.json`** → không thì picker không hiện model.
5. **`config.yaml default` có thể bị revert** → verify trên disk trước khi restart.

## 📊 Model test trên hcnsec.cn (2026-07-19)

✅ Chạy: auto, DeepSeek-V4-Flash*, DeepSeek-V4-Pro, glm-5.2*, Kimi-K2.6, MiniMax-M2.7/3, Qwen3.5/3.6, step-3.5/3.7-flash, kat-coder-pro-v2/v2.5, sensenova-*, step-router-v1
❌ Không: glm-4.7 (invalid), glm-5.1 (not_found), Spark-X2-Flash (invalid)
(*) = content rỗng do thinking mode → cần tắt

---
*Tested on Lysa/agent2 — Hermes Agent by Nous Research*
