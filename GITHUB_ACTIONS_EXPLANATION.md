# 📚 Penjelasan GitHub Actions

## 🤔 Apa itu GitHub Actions?

**GitHub Actions** adalah platform CI/CD (Continuous Integration/Continuous Deployment) yang terintegrasi langsung di GitHub. Dengan GitHub Actions, kita dapat mengotomatisasi workflow pengembangan software seperti:
- ✅ Testing otomatis
- 🏗️ Building aplikasi
- 🚀 Deployment ke server/hosting
- 📦 Publishing packages
- 🔄 Dan automation lainnya

## 📂 Struktur Folder GitHub Actions

Workflow GitHub Actions harus disimpan di folder khusus:
```
.github/
  └── workflows/
      └── main.yml  (atau nama file .yml lainnya)
```

## 🔍 Analisis Workflow Laundry Santuy

### File: `.github/workflows/main.yml`

> **✅ UPDATE (March 2, 2026)**: Workflow telah disesuaikan dengan kebutuhan proyek yang sebenarnya

### Workflow Baru yang Sesuai Kebutuhan:

```yaml
name: Documentation & CI | Laundry Santuy

on:
  push:
    branches: [main]
  pull_request:
    branches: [main]

jobs:
  # Job 1: Deploy Documentation (AKTIF)
  deploy-docs:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: peaceiris/actions-gh-pages@v3
        with:
          github_token: ${{ secrets.GITHUB_TOKEN }}
          publish_dir: ./docs
  
  # Job 2: Validate Project (AKTIF)
  validate-project:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - name: Check Required Files
        run: |
          # Validasi struktur project
          
  # Job 3: Frontend Testing (COMMENTED - untuk nanti)
  # test-frontend: ...
  
  # Job 4: Backend Testing (COMMENTED - untuk nanti)
  # test-backend: ...
```

### 📋 Apa yang Berubah?

#### ❌ Workflow Lama (Salah):
```yaml
jobs:
  test-build:
    steps:
      - run: npm i          # ❌ Tidak ada package.json
      - run: npm run build  # ❌ Tidak ada build script
```

**Masalah:**
- Mengasumsikan ada Node.js project
- Tidak sesuai dengan kondisi proyek saat ini
- Copy-paste tanpa menyesuaikan kebutuhan

#### ✅ Workflow Baru (Benar):
```yaml
jobs:
  deploy-docs:            # ✅ Deploy dokumentasi yang ADA
  validate-project:       # ✅ Validasi file penting
  # test-frontend:        # ⏸️ Siap untuk nanti
  # test-backend:         # ⏸️ Siap untuk nanti
```

**Kenapa Benar:**
- Sesuai dengan kondisi proyek **saat ini** (dokumentasi)
- Siap untuk **future development** (frontend/backend)
- Tidak running test yang tidak ada kodenya

### 📋 Penjelasan Komponen Workflow Baru:

#### 1. **`name`**
```yaml
name: Documentation & CI | Laundry Santuy
```
- Nama workflow yang mencerminkan fungsi sebenarnya
- **Documentation**: Deploy dokumentasi GitHub Pages
- **CI**: Continuous Integration untuk validasi project

#### 2. **`on`** (Trigger)
```yaml
on:
  push:
    branches: [main]
  pull_request:
    branches: [main]
```
- **`push`**: Workflow jalan saat push ke branch `main`
- **`pull_request`**: Workflow jalan saat ada PR ke `main` (untuk review sebelum merge)

#### 3. **`jobs`** - Jobs yang Aktif

##### Job 1: `deploy-docs` ✅ AKTIF
```yaml
deploy-docs:
  name: Deploy Documentation
  runs-on: ubuntu-latest
  steps:
    - uses: actions/checkout@v4
    - uses: peaceiris/actions-gh-pages@v3
      if: github.ref == 'refs/heads/main'
      with:
        github_token: ${{ secrets.GITHUB_TOKEN }}
        publish_dir: ./docs
```

**Fungsi:**
- Deploy folder `docs/` ke GitHub Pages
- Hanya jalan untuk push ke `main` (bukan PR)
- Menggunakan action `peaceiris/actions-gh-pages@v3`

**Kenapa perlu:**
- Proyek sudah punya dokumentasi di `docs/index.md`
- Dokumentasi perlu di-publish agar bisa diakses publik
- Otomatis update setiap kali ada perubahan dokumentasi

##### Job 2: `validate-project` ✅ AKTIF
```yaml
validate-project:
  name: Validate Project Structure
  runs-on: ubuntu-latest
  steps:
    - uses: actions/checkout@v4
    - name: Check Required Files
      run: |
        # Check documentation files
        if [ -f "README.md" ]; then
          echo "✅ README.md exists"
        else
          echo "❌ README.md missing"
          exit 1
        fi
        # ... validasi file lainnya
```

**Fungsi:**
- Validasi file-file penting: `README.md`, `PROJECT_BREAKDOWN.md`, `docs/`
- Early detection jika ada file penting yang terhapus
- Quality check untuk struktur project

**Kenapa perlu:**
- Memastikan dokumentasi penting tidak hilang
- Mencegah merge yang merusak struktur project
- Best practice untuk project management

#### 4. **`jobs`** - Jobs yang Dinonaktifkan (Future)

##### Job 3: `test-frontend` ⏸️ COMMENTED
```yaml
# test-frontend:
#   name: Test & Build Frontend
#   strategy:
#     matrix:
#       node-version: [18.x, 20.x]
#   steps:
#     - run: npm ci
#     - run: npm run lint
#     - run: npm test
#     - run: npm run build
```

**Kapan diaktifkan:**
- Saat frontend development dimulai
- Sudah ada `package.json` dan dependencies
- Sudah ada unit tests dan linter

##### Job 4: `test-backend` ⏸️ COMMENTED
```yaml
# test-backend:
#   name: Test Backend API
#   steps:
#     - uses: actions/setup-python@v4
#     - run: pip install -r requirements.txt
#     - run: pytest --cov
```

**Kapan diaktifkan:**
- Saat backend development dimulai
- Sudah ada `requirements.txt` atau `package.json`
- Sudah ada unit tests untuk API

## 📊 Keadaan GitHub Actions Saat Ini

Berdasarkan screenshot GitHub Anda:

### Workflow yang Terlihat:
1. **pages-build-deployment** (Otomatis dari GitHub Pages)
   - ✅ 3 workflow run berhasil (hijau)
   - ❌ 1 workflow run gagal (merah)
   - ⏳ 1 workflow run sedang berjalan/pending (abu-abu)

### Status Workflow:
- **5 workflow runs**: Total eksekusi workflow
- **main branch**: Semua workflow berjalan di branch main
- **Duration**: Berkisar antara 28-59 detik

## 🔄 Alur Kerja GitHub Actions

```
Developer Push Code
        ↓
   GitHub Detects Push
        ↓
   Trigger Workflow (on: push)
        ↓
   Spin Up Runner (ubuntu-latest)
        ↓
   Execute Steps:
   1. Checkout code
   2. Setup Node.js
   3. Install dependencies (npm i)
   4. Build project (npm run build)
        ↓
   Workflow Complete ✅
   (atau Failed ❌)
```

## 🚀 Manfaat GitHub Actions untuk Laundry Santuy

1. **Automated Testing**: Setiap kali push code, otomatis ter-build dan tertest
2. **Early Bug Detection**: Jika build gagal, langsung terdeteksi sebelum deploy
3. **Consistent Environment**: Selalu ditest di environment yang sama (ubuntu-latest)
4. **Time Saving**: Tidak perlu manual testing setiap kali ada perubahan
5. **GitHub Pages Deploy**: Otomatis deploy dokumentasi ke GitHub Pages

## 📝 Catatan Penting

### ✅ Issues yang Sudah Diperbaiki

#### 1. Folder Name Typo
~~Dulu: `.github/wokrflows/main.yml` ❌~~  
**Sekarang: `.github/workflows/main.yml` ✅**

#### 2. Workflow Configuration
~~Dulu: Generic Node.js workflow yang tidak sesuai kebutuhan ❌~~  
**Sekarang: Workflow yang sesuai kondisi proyek saat ini ✅**

### 📌 Workflow Sekarang Sudah Tepat Karena:

1. ✅ **Sesuai kondisi proyek saat ini** - Deploy dokumentasi yang memang sudah ada
2. ✅ **Ada validasi project** - Memastikan file penting tidak hilang
3. ✅ **Siap untuk development** - Jobs future sudah disiapkan (commented)
4. ✅ **Tidak ada test yang gagal** - Tidak run npm/pip yang belum ada
5. ✅ **Mengikuti Lab 2.5 Point #15** - Disesuaikan dengan kebutuhan kelompok

### 🎯 Roadmap Workflow

#### Phase 1: Documentation (SAAT INI) ✅
```yaml
✅ deploy-docs        # Deploy docs ke GitHub Pages
✅ validate-project   # Validasi file penting
```

#### Phase 2: Development (NANTI)
```yaml
✅ deploy-docs
✅ validate-project
✅ test-frontend      # Uncomment saat frontend siap
✅ test-backend       # Uncomment saat backend siap
```

#### Phase 3: Production (FUTURE)
```yaml
✅ deploy-docs
✅ validate-project
✅ test-frontend
✅ test-backend
✅ integration-test   # Tambahkan job baru
✅ deploy-production  # Tambahkan deployment
```

## 🎯 Kesimpulan

**GitHub Actions** adalah automation tool yang membantu tim Laundry Santuy untuk:
- Memastikan code quality dengan automated testing
- Mempercepat development cycle
- Deploy dokumentasi/aplikasi secara otomatis
- Meningkatkan kolaborasi tim dengan CI/CD pipeline

### ✅ Status Workflow Laundry Santuy

**Workflow sekarang sudah TEPAT dan SESUAI kebutuhan!**

#### Yang Sudah Selesai:
- ✅ Folder typo diperbaiki: `workflows` (bukan `wokrflows`)
- ✅ Workflow disesuaikan dengan kondisi proyek saat ini
- ✅ Deploy dokumentasi ke GitHub Pages
- ✅ Validasi struktur project
- ✅ Siap untuk future development (frontend/backend)

### ⚡ Next Steps untuk Laundry Santuy:

#### 1. Selesaikan Lab 2.5 (Project Management)
- [ ] Lengkapi [`PROJECT_BREAKDOWN.md`](PROJECT_BREAKDOWN.md)
- [ ] Buat GitHub Project dengan template "Team Backlog"
- [ ] Convert actionables menjadi Issues
- [ ] Assign issues ke anggota tim
- [ ] Screenshot untuk worksheet

#### 2. GitHub Actions Monitoring
- [ ] Push changes dan cek workflow di tab **Actions**
- [ ] Pastikan job `deploy-docs` berhasil (green ✅)
- [ ] Pastikan job `validate-project` berhasil (green ✅)
- [ ] Akses dokumentasi di GitHub Pages: `https://[username].github.io/laundry-santuy/`
- [ ] Screenshot hasil workflow untuk worksheet Lab 2.5

#### 3. Development Phase
Ketika mulai development:

**Frontend (React/Vue/Angular):**
```bash
# 1. Buat package.json
npm init

# 2. Install dependencies
npm install

# 3. Uncomment job test-frontend di .github/workflows/main.yml
```

**Backend (Python/Node.js):**
```bash
# 1. Buat requirements.txt atau package.json
# 2. Setup testing framework
# 3. Uncomment job test-backend di .github/workflows/main.yml
```

### 📚 Dokumentasi Lengkap

Untuk detail lebih lanjut tentang workflow:
- 📄 [`.github/workflows/README.md`](.github/workflows/README.md) - Dokumentasi workflow lengkap
- 📄 [`PROJECT_BREAKDOWN.md`](PROJECT_BREAKDOWN.md) - Template breakdown proyek
- 📄 [`GITHUB_ACTIONS_EXPLANATION.md`](GITHUB_ACTIONS_EXPLANATION.md) - File ini

---

> ✅ **Workflow sudah SIAP dan SESUAI kebutuhan proyek Laundry Santuy!**
> 
> **Kelompok Random** | Senior Project 2025/2026  
> Universitas Gadjah Mada - DTETI
