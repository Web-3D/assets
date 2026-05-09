# assets/ — Thư viện 3D Assets
> Shared asset library cho toàn bộ workspace `THREEJS/`.
> Tái sử dụng qua nhiều project — không duplicate file giữa các project.

---

## Vai trò trong workspace

```
THREEJS/
├── 00-Threejs/      ← project (chỉ import từ assets/[category]/[name]/production/)
├── threejs-modules/ ← code library (shaders, utils)
└── assets/          ← asset library — repo git riêng, versioning độc lập
```

---

## Cấu trúc thư mục

```
assets/
├── README.md                    ← file này
├── ARCHITECTURE.md              ← (deprecated — đã gộp vào đây)
├── .gitattributes               ← Git LFS tracking: *.glb *.spz *.ktx2 *.png *.exr
│
├── buildings/                   ← công trình kiến trúc
│   ├── _template/meta.json      ← schema mẫu cho category này
│   └── [asset-name]/
│       ├── meta.json
│       ├── raw/                 ← output thô từ AI — KHÔNG SỬA
│       ├── optimized/           ← sau Blender MCP cleanup
│       └── production/          ← sau gltf-transform — PROJECT DÙNG CÁI NÀY
│
├── characters/                  ← nhân vật NPC / hero
│   ├── _template/meta.json      ← thêm fields: rig, animations
│   └── [asset-name]/  (raw/ + optimized/ + production/)
│
├── environments/                ← background Gaussian Splat (Luma AI)
│   ├── _template/meta.json      ← format: spz, không có optimized/
│   └── [asset-name]/
│       ├── meta.json
│       ├── raw/                 ← .ply từ Luma AI
│       └── production/          ← .spz compressed (không có optimized/)
│
├── props/                       ← đồ vật nhỏ (ghế, xe, biển hiệu...)
│   ├── _template/meta.json      ← như buildings nhưng texture-size: 512x512
│   └── [asset-name]/  (raw/ + optimized/ + production/)
│
└── textures/                    ← PBR texture library dùng chung
    ├── _template/meta.json      ← schema khác: source, resolution, maps, license
    └── [texture-name]/          ← flat structure, không có raw/optimized/production
        ├── meta.json
        ├── diffuse_2k.ktx2
        ├── normal_2k.ktx2
        └── roughness_2k.ktx2
```

---

## Pipeline 3 trạng thái

```
raw/  ──────────────>  optimized/  ──────────>  production/
AI tool                Blender MCP              gltf-transform
(Tripo/Meshy/Rodin)    decimate, UV,            Draco + KTX2,
KHÔNG SỬA GÌ           bake, origin             browser-ready
```

| Trạng thái    | Tạo bởi    | Được sửa | Dùng trong project |
|------------   |---------     |----------  |-------------------|
| `raw/`        | AI tool      | ❌ KHÔNG   | ❌ |
| `optimized/`  | Blender MCP  | ✅ iterate   | ❌ |
| `production/` | gltf-transform | ❌ KHÔNG   | ✅ | 

**Quy tắc vàng:** Project chỉ import từ `production/`. Không shortcut.

### Ngoại lệ environments/

Splat không qua Blender MCP — không có `optimized/`:
```
environments/[name]/raw/         ← .ply từ Luma AI
environments/[name]/production/  ← .spz compressed
```

### Ngoại lệ textures/

Từ Poly Haven — đã tối ưu sẵn, flat structure không có 3 trạng thái.

---

## meta.json schema theo category

### buildings

```json
{
  "name": "quan-ca-phe-goc-pho",
  "category": "buildings",
  "source-tool": "meshy",
  "status": "production",
  "description": "Quán cà phê góc phố phong cách Việt Nam cũ",
  "tags": ["vietnamese", "cafe", "street"],
  "polycount": { "raw": 45000, "optimized": 8200, "production": 8200 },
  "texture-size": "2048x2048",
  "file-size": { "raw": "12.4MB", "optimized": "3.1MB", "production": "0.8MB" },
  "used-in": ["00-Threejs"],
  "created": "2026-05-08",
  "notes": ""
}
```

### props

Như buildings nhưng `"texture-size": "512x512"` (prop nhỏ không cần texture lớn).

### characters

```json
{
  "name": "npc-vendor-female",
  "category": "characters",
  "source-tool": "rodin",
  "status": "production",
  "description": "NPC người bán hàng nữ, trang phục Việt Nam",
  "tags": ["npc", "female", "vendor"],
  "polycount": { "raw": 85000, "optimized": 9800, "production": 9800 },
  "texture-size": "1024x1024",
  "file-size": { "raw": "28MB", "optimized": "4.2MB", "production": "1.1MB" },
  "rig": false,
  "animations": [],
  "used-in": [],
  "created": "2026-05-08",
  "notes": ""
}
```

### environments

```json
{
  "name": "pho-co-hanoi-sunset",
  "category": "environments",
  "source-tool": "luma",
  "status": "production",
  "description": "Phố cổ Hà Nội lúc hoàng hôn, Gaussian Splat",
  "tags": ["hanoi", "sunset", "splat"],
  "format": "spz",
  "capture-location": "Hoàn Kiếm, Hà Nội",
  "file-size": { "raw": "450MB", "production": "85MB" },
  "used-in": [],
  "created": "2026-05-08",
  "notes": ""
}
```

### textures

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

## Performance budget

| Asset type | Polycount production | Texture max |
|-----------|---------------------|-------------|
| Prop nhỏ | < 500 tris | 512×512 |
| Building | < 5,000 tris | 2048×2048 |
| NPC character | < 10,000 tris | 1024×1024 |
| Hero character | < 30,000 tris | 2048×2048 |

Asset không đạt budget → không move vào `production/`.

---

## Tool → Category mapping

| Category | AI Generation | Blender MCP | gltf-transform |
|----------|--------------|-------------|----------------|
| `buildings` | Tripo / Meshy | ✅ cleanup + UV | ✅ Draco + KTX2 |
| `props` | Tripo | ✅ cleanup | ✅ Draco + KTX2 |
| `characters` | Rodin / Tripo | ✅ rig + VAT bake | ✅ mesh only |
| `environments` | Luma AI | ❌ skip | ✅ .ply → .spz |
| `textures` | Poly Haven | ❌ skip | ✅ KTX2 convert |

---

## Git strategy

`assets/` là git repo riêng — binary lớn không commit cùng code:

```
.gitattributes  ← Git LFS: *.glb filter=lfs, *.spz filter=lfs, *.ktx2 filter=lfs
```

Asset > 5MB → CDN (Cloudflare R2 / Bunny.net). Asset < 5MB → bundle với Vite.

---

## Bộ luật không ngoại lệ

1. **Không sửa `raw/`** — giữ nguyên output AI để diff và re-optimize sau.
2. **Project chỉ import từ `production/`** — không shortcut.
3. **`meta.json` bắt buộc** — asset không có meta.json là asset chưa hợp lệ.
4. **Tên folder = kebab-case** — `quan-ca-phe`, không phải `QuanCaPhe` hay `quan_ca_phe`.
5. **Không duplicate** — 2 project cần cùng asset → cả 2 reference cùng `production/`.

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
- [ ] Tạo `meta.json` với đủ field bắt buộc (xem schema theo category ở trên)
- [ ] Đặt file AI output vào `raw/` — không sửa gì
- [ ] Chạy Blender MCP Workflow A → lưu vào `optimized/`, update `meta.status: "optimized"`
- [ ] Chạy gltf-transform → lưu vào `production/`, update `meta.status: "production"`
- [ ] Update `polycount.production`, `texture-size`, `bounds`, `file-size.production` trong `meta.json`
- [ ] **Chạy `node validate.js assets/[category]/[name]`** — phải PASS trước khi dùng
- [ ] Update bảng Asset Catalog trong file này
- [ ] Update `used-in` trong `meta.json` khi import vào project

**Blender MCP Workflow A (cleanup chuẩn):**
```
Import [path]/raw/[file].glb. Sau đó:
1. Decimate xuống [X]k polygons giữ silhouette
2. UV unwrap lại với seam logical
3. Bake AO map 2048x2048 to texture
4. Recalculate normals outside
5. Set origin to bottom-center (building) hoặc center (prop)
6. Export GLTF với Draco compression, texture KTX2
7. Save tới [path]/optimized/[file].glb
```
