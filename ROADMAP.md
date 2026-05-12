# assets/ — Roadmap & Tiến độ

> File này track kế hoạch và trạng thái build asset library.
> Cập nhật thủ công sau mỗi milestone. REGISTRY.json là source of truth cho trạng thái kỹ thuật.

---

## Trạng thái hiện tại

| --------------------------- | --------------------------------------------------------------- |
| --------------------------- | --------------------------------------------------------------- |
| **Phase**                   | Phase A — Environment Foundation                                |
| **Bắt đầu**                 | 2026-05-11                                                      |
| **Mục tiêu Phase A**        | 1 environment splat + 2–3 buildings standard + 3–5 textures PBR |
| **Assets production-ready** | 0 / ~8 planned                                                  |

---

## Kế hoạch theo Phase

### Phase A — Environment Foundation _(hiện tại)_

Mục tiêu: đủ asset để test toàn bộ shader system — TriplanarMapping, WorldNoise, GlobalUniforms.

| Asset                 | Category     | Tier     | Priority    | Status  | Notes                                            |
| --------------------- | ------------ | -------- | ----------- | ------- | ------------------------------------------------ |
| `pho-co-hanoi-01`     | environments | —        | 🔴 Critical | planned | Splat Luma AI — background chính                 |
| `quan-ca-phe-goc-pho` | buildings    | standard | 🔴 Critical | planned | Test TriplanarMapping + vietnamese-street shader |
| `nha-pho-tuong-co`    | buildings    | standard | 🟡 Nice     | planned | Test WorldNoise trên bề mặt tường                |
| `xe-may-dung-via-he`  | props        | standard | 🟡 Nice     | planned | Prop đơn giản, test shadow                       |
| `stone-cobble-vn-01`  | textures     | —        | 🔴 Critical | planned | Cần cho TriplanarMapping — vỉa hè đá             |
| `plaster-worn-01`     | textures     | —        | 🔴 Critical | planned | Tường vữa cũ — nhà Việt                          |
| `roof-tile-vn-01`     | textures     | —        | 🟡 Nice     | planned | Ngói Việt Nam                                    |

### Phase B — Character Integration _(tương lai)_

| Asset                  | Category   | Tier | Priority    | Status      | Notes                        |
| ---------------------- | ---------- | ---- | ----------- | ----------- | ---------------------------- |
| `npc-vendor-female-01` | characters | npc  | 🔴 Critical | not started | NPC người bán hàng, Rodin    |
| `npc-customer-male-01` | characters | npc  | 🟡 Nice     | not started | NPC khách hàng               |
| `hero-player-01`       | characters | hero | 🟡 Nice     | not started | Player character — VAT baked |

### Phase C — Full Scene _(tương lai)_

Bổ sung khi Phase B xong — hero buildings, multi-environment, character variants.

---

## Budget per Tier

### Buildings

| Tier       | Polycount production | Texture max | Ví dụ                                 |
| ---------- | -------------------- | ----------- | ------------------------------------- |
| `standard` | ≤ 5,000 tris         | 2048×2048   | Hàng quán ven đường, nhà phố thường   |
| `medium`   | ≤ 30,000 tris        | 2048×2048   | Nhà chính, tòa nhà quan trọng         |
| `hero`     | ≤ 100,000 tris       | 4096×4096   | Focal point — chùa, cột mốc, landmark |

### Characters

| Tier   | Polycount production | Texture max |
| ------ | -------------------- | ----------- |
| `npc`  | ≤ 15,000 tris        | 1024×1024   |
| `hero` | ≤ 50,000 tris        | 2048×2048   |

### Props

| Tier       | Polycount production | Texture max |
| ---------- | -------------------- | ----------- |
| `standard` | ≤ 2,000 tris         | 512×512     |

### Environments & Textures

Không có budget tris — splat và texture không validate polycount.

---

## ShaderProfile Registry

Field `shaderProfile` trong `meta.json` link asset với climate/material identity.
Được đọc bởi `GlobalUniforms` để inject đúng uniform set.

| shaderProfile       | Climate uniforms kích hoạt                                 | Dùng khi                                              |
| ------------------- | ---------------------------------------------------------- | ----------------------------------------------------- |
| `vietnamese-street` | uTime, uWeather, uHumidity, uDamage, uTemperature, uSeason | Công trình phố Việt Nam — nắng nhiệt đới, mưa, patina |
| `tropical-nature`   | uTime, uWeather, uHumidity, uTemperature                   | Cây cối, thiên nhiên nhiệt đới                        |
| `interior`          | uTime, uDamage                                             | Nội thất — ánh sáng nhân tạo, wear                    |
| `neutral`           | uTime                                                      | Generic — không cần regional identity                 |
| `splat-outdoor`     | uTime, uWeather                                            | Environment splat — thời tiết ảnh hưởng density       |

---

## Changelog

| Ngày       | Thay đổi                                                                       |
| ---------- | ------------------------------------------------------------------------------ |
| 2026-05-11 | Khởi tạo ROADMAP.md — budget tier system, shaderProfile registry, Phase A plan |
