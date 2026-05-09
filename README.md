# assets/ — Thư viện 3D Assets

> Shared asset library cho toàn bộ workspace `THREEJS/`.
> Tái sử dụng qua nhiều project — không duplicate file giữa các project.

---

## Cấu trúc thư mục

```
assets/
├── README.md              ← file này — catalog + bộ luật
├── buildings/             ← công trình (nhà, quán, tòa nhà)
├── characters/            ← nhân vật (NPC, hero)
├── environments/          ← background splat (Luma AI .spz)
├── props/                 ← đồ vật nhỏ (ghế, bảng, xe...)
└── textures/              ← PBR texture library dùng chung
```

Mỗi asset là 1 folder con trong category:

```
assets/[category]/[asset-name]/
├── meta.json              ← metadata bắt buộc
├── raw/                   ← output thô từ AI tool, KHÔNG sửa
├── optimized/             ← sau Blender MCP cleanup
└── production/            ← sau gltf-transform, ready cho browser
```

---

## 3 Trạng thái asset (pipeline)

```
raw/  →  optimized/  →  production/
 AI         Blender MCP      gltf-transform
 tool       (decimate,       (Draco + KTX2,
            UV, origin)      browser-ready)
```

| Trạng thái | Ai tạo | Được sửa không | Dùng trong project |
|-----------|--------|---------------|-------------------|
| `raw/` | AI tool (Tripo/Meshy/Rodin/Luma) | ❌ KHÔNG — giữ nguyên để diff | ❌ |
| `optimized/` | Blender MCP | ✅ Có thể iterate | ❌ |
| `production/` | gltf-transform | ❌ KHÔNG — output cuối | ✅ Import vào project |

**Quy tắc vàng:** Project chỉ dùng file từ `production/`. Không bao giờ import từ `raw/` hoặc `optimized/`.

---

## `meta.json` schema

Mỗi asset **bắt buộc** có `meta.json` ở root folder của asset:

```json
{
  "name": "ten-asset-kebab-case",
  "category": "buildings",
  "source-tool": "tripo",
  "status": "optimized",
  "description": "Mô tả 1 câu — dùng để search",
  "tags": ["vietnamese", "cafe", "street"],
  "polycount": {
    "raw": 45000,
    "optimized": 8200,
    "production": 8200
  },
  "texture-size": "2048x2048",
  "file-size": {
    "raw": "12.4MB",
    "optimized": "3.1MB",
    "production": "0.8MB"
  },
  "used-in": ["00-Threejs"],
  "created": "2026-05-08",
  "notes": ""
}
```

### Field bắt buộc

| Field | Values | Ghi chú |
|-------|--------|---------|
| `name` | kebab-case string | Trùng với tên folder |
| `category` | `buildings` / `characters` / `environments` / `props` / `textures` | |
| `source-tool` | `tripo` / `meshy` / `rodin` / `luma` / `blender` / `polycam` | Tool tạo ra file raw |
| `status` | `raw` / `optimized` / `production` | Trạng thái cao nhất hiện có |
| `description` | string | 1 câu ngắn |
| `created` | `YYYY-MM-DD` | Ngày tạo |

### Field khuyến nghị

| Field | Ghi chú |
|-------|---------|
| `tags` | Giúp search — đừng để trống |
| `polycount` | Điền khi có số, để `null` nếu chưa biết |
| `used-in` | Project nào đang dùng asset này |
| `notes` | Vấn đề biết trước (UV xấu, seam...) |

---

## Performance Budget (giống CLAUDE.md)

| Loại asset | Polycount production | Texture max |
|-----------|---------------------|-------------|
| Prop nhỏ | < 500 tris | 512×512 |
| Building | < 5,000 tris | 2048×2048 |
| Hero object | < 20,000 tris | 2048×2048 |
| Character NPC | < 10,000 tris | 1024×1024 |
| Character hero | < 30,000 tris | 2048×2048 |

Asset **không đạt budget** → không được move vào `production/`.

---

## Textures category

`textures/` không theo cấu trúc `raw/optimized/production` — chỉ có 1 level:

```
textures/
├── stone-cobble-01/
│   ├── meta.json
│   ├── diffuse_2k.ktx2
│   ├── normal_2k.ktx2
│   └── roughness_2k.ktx2
├── wood-plank-02/
│   └── ...
```

Texture `meta.json` schema đơn giản hơn:

```json
{
  "name": "stone-cobble-01",
  "category": "textures",
  "source": "poly-haven",
  "resolution": "2048x2048",
  "maps": ["diffuse", "normal", "roughness"],
  "format": "ktx2",
  "used-in": ["TriplanarMapping"],
  "license": "CC0"
}
```

---

## Bộ luật không ngoại lệ

1. **Không sửa `raw/`** — giữ nguyên output từ AI tool để có thể so sánh và re-optimize sau.
2. **Project chỉ import từ `production/`** — không shortcut.
3. **`meta.json` bắt buộc** — asset không có meta.json là asset chưa hợp lệ.
4. **Tên folder = kebab-case** — `quan-ca-phe`, không phải `QuanCaPhe` hay `quan_ca_phe`.
5. **Không duplicate** — nếu 2 project cần cùng 1 asset, cả 2 reference đến cùng `production/` file.

---

## Asset Catalog

### Buildings

| Asset | Source | Status | Polycount (prod) | Used in |
|-------|--------|--------|-----------------|---------|
| _(chưa có)_ | — | — | — | — |

### Characters

| Asset | Source | Status | Polycount (prod) | Used in |
|-------|--------|--------|-----------------|---------|
| _(chưa có)_ | — | — | — | — |

### Environments

| Asset | Source | Status | Format | Used in |
|-------|--------|--------|--------|---------|
| _(chưa có)_ | — | — | — | — |

### Props

| Asset | Source | Status | Polycount (prod) | Used in |
|-------|--------|--------|-----------------|---------|
| _(chưa có)_ | — | — | — | — |

### Textures

| Texture | Source | Resolution | Maps | Used in |
|---------|--------|-----------|------|---------|
| _(chưa có)_ | — | — | — | — |

---

## Thêm asset mới — checklist

- [ ] Tạo folder `assets/[category]/[asset-name]/`
- [ ] Tạo `meta.json` với đủ field bắt buộc
- [ ] Đặt file AI output vào `raw/` — không sửa gì
- [ ] Chạy Blender MCP cleanup → lưu vào `optimized/`
- [ ] Chạy gltf-transform pipeline → lưu vào `production/`
- [ ] Verify polycount đạt budget trước khi move vào `production/`
- [ ] Update bảng catalog trong README này
- [ ] Update `used-in` trong `meta.json` khi import vào project
