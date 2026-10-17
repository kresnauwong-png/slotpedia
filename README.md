# SlotPedia — Katalog Game Slot Pragmatic Play & PG Soft

Website katalog game slot (Pragmatic Play + PG Soft) dengan info RTP teoretis dan **panel admin** untuk mengatur konten & pola permainan. Murni front-end (HTML/CSS/JS), tanpa backend.

## Fitur

- **Katalog game** — pencarian, filter provider, pengurutan (RTP, nama, tahun), kartu gradasi unik, modal detail.
- **Info RTP** — tabel terurut dengan bar perbandingan + disclaimer tentang RNG.
- **Pola permainan** — section publik yang diisi admin melalui panel admin. Draft bisa disimpan tanpa tampil.
- **Mode Demo (iFrame)** — tombol "Coba Demo" memuat game demo asli via URL. **19 game sudah terisi otomatis**: 10 Pragmatic Play (loader resmi `demogamesfree.pragmaticplay.net/gs2c/openGame.do?gameSymbol=...`) dan 9 PG Soft (pemutar resmi `public.pg-demo.com/demo/?gi=...`). Semua terverifikasi bisa di-iframe. Sisa game (Candy Burst, Shaolin Spinners) belum ada URL demo publik — admin bisa mengisinya di panel admin (kolom "URL demo"). Jika kosong, muncul info saja.
- **Panel Admin** (`⚙️`):
  - Login dengan password (verifikasi SHA-256/fnv + session)
  - **CRUD game** — tambah/edit/hapus game beserta RTP, volatilitas, max win, tag, deskripsi, dan **URL demo (iframe)**
  - **CRUD pola permainan** — tentukan game, langkah pola (per baris), catatan, status aktif/draft
  - **Backup** — Export JSON / Import JSON, reset ke default
  - Data tersimpan di `localStorage` browser, jadi perubahan admin bersifat persisten
- **Akun demo** — Daftar/Masuk yang benar-benar berfungsi (username + password, tersimpan di `localStorage` browser, hash FNV-1a). Sesi login dipulihkan saat reload.
- **Arena Main Demo** — mesin slot simulasi 5×3 (3 payline) dengan **kredit virtual**: taruhan, animasi putaran, perhitungan menang, riwayat putaran, tombol "Tambah 100.000" kredit demo. Saldo awal 100.000 kredit.
- **1 file mandiri** — `node build.js` menghasilkan satu file HTML yang bisa di-host di mana saja.
- Responsif / dark theme / aksesibel keyboard.

## Struktur

```
slot-site/
├── index.html        # halaman utama (sumber, multi-file)
├── css/style.css
├── js/
│   ├── data.js       # data game + pola default
│   └── app.js        # render, filter, panel admin, pola, backup
├── build.js          # script gabung jadi 1 file -> dist/index.html
├── dist/index.html   # HASIL BUILD: file tunggal siap pakai
└── README.md
```

Pengembangan dilakukan pada file sumber (`index.html`, `css/`, `js/`). Setelah mengubah sumber, jalankan build:

```bash
node build.js        # hasil: dist/index.html
```

Untuk tes online singkat: buka `dist/index.html` langsung, atau jalankan `python3 -m http.server 8000` dari folder `dist`.

## Panel Admin

Cara pakai:

1. Scaffold footer → klik **Admin** (tautan kecil di kolom "Informasi").
2. Masukkan password default: **`SlotPedia2026`**
3. Kelola game & pola lewat tab **Game / Pola / Data & Backup**.

**Ganti password:** edit di `js/app.js` dua konstanta `ADMIN_SHA256` dan `ADMIN_FNV` (FNV-1a 32-bit), lalu `node build.js`. Hitung hash dengan:

```bash
node -e "const c=require('crypto');console.log(c.createHash('sha256').update('PASSWORD_BARU').digest('hex'))"
```

dan untuk FNV tambahkan perhitungan pendukung (lihat app.js). Ingat: karena autentikasi berjalan di browser, password & hashnya SELALU bisa dibaca orang dari source — ini hanya penyaring ringan, bukan keamanan nyata.

**Cara menambah game / pola lewat kode:** tambahkan objek ke `GAMES` (data.js), atau cukup pakai panel admin lalu **Export JSON** untuk menyimpan perubahan ke file.

## Menambah data default

Tambahkan objek di `js/data.js`:

```js
{
  id: "nama-unik", name: "Nama Game", provider: "pragmatic", // atau "pgsoft"
  rtp: 96.5, volatility: "Tinggi", maxWin: "5.000x",
  grid: "6×5", year: 2024, tags: ["Tumble", "Buy Spin"],
  desc: "Deskripsi singkat.",
},
```

## Deploy ke Netlify

Persyaratan: akun Netlify (gratis).

**Cara termudah — drag & drop:**
1. Buka <https://app.netlify.com/drop>
2. Drag folder `dist/` ke area tersebut → selesai, langsung dapat URL `https://<random>.netlify.app`.

**Dengan Netlify CLI:**
```bash
npm i -g netlify-cli
netlify deploy --dir dist --prod    # login pertama kali akan diminta melalui browser
```

Hosting apapun juga bisa (GitHub Pages, Vercel, Cloudflare Pages, Hostinger, dll.) — cukup upload isi folder `dist`.

## Catatan penting

1. **Tidak ada transaksi uang asli.** Akun & "Arena Main Demo" menggunakan **kredit virtual** yang disimpan di browser (localStorage) — bukan uang, tidak ada deposit/penarikan/pembayaran. Memproses uang asli butuh backend, KYC, payment gateway, dan **lisensi perjudian sah** — di luar lingkup proyek ini. Login akun berjalan di browser, jadi hash password bisa dibaca dari source: ini penyaring demo, bukan keamanan nyata.
2. **RTP & pola.** RTP yang ditampilkan adalah angka teoretis publik dari provider. "Pola permainan" adalah kebiasaan bermain yang disusun admin — **bukan prediksi hasil RNG**. Disclaimer wajib tampil di section Pola dan tidak bisa dimatikan.
3. **Storage.** Data admin tersimpan di `localStorage` tiap browser. Untuk berbagi ke pengunjung lain, pakai Export/Import JSON, atau tanam data default di `data.js` lalu build ulang. Browser tertentu (mis. Firefox) memblokir localStorage untuk file `file://` — host di HTTP/HTTPS agar fitur tersimpan berjalan penuh.

## Test

- `test.js` / `test2.js` (jsdom) di folder dev — menjalankan halaman via server HTTP lokal. `test2.js` mencakup 64 asersi: render (21 game), filter, pencarian, sort, modal, daftar/masuk akun demo (sukses/gagal/duplikat/logout), arena slot (render 15 sel, taruhan, spin menang & kalah deterministik, saldo, riwayat), login admin, CRUD game & pola, draft vs publik, persistensi localStorage + simulasi reload, hapus berantai, import/export, logout. Status: **semua PASS**.