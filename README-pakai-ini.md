SlotPedia - deploy single file
============================

1. Ekstrak -> folder berisi index.html
2. Host di HTTP/HTTPS (Netlify drop: drag folder ini).
3. Agar tombol 'Coba Demo' memuat game: login panel admin -> tab Game -> isi kolom
   'URL demo (iframe)' per game dengan URL demo resmi (http/https). Tombol akan
   menampilkan info saja bila URL belum diisi.

BARU - URL demo terisi otomatis:
- 10 game Pragmatic Play sudah punya URL demo resmi (langsung bisa dimainkan
  lewat tombol "Coba Demo").
- 11 game PG Soft belum ada URL demo publik yang stabil; isi manual di
  Panel Admin -> tab Game -> kolom "URL demo (iframe)" (harus http/https).

BARU - Akun & Arena Main Demo:
- Klik "Daftar" untuk buat akun demo (tersimpan di browser, bukan server).
- Section "Arena Main Demo": mesin slot simulasi 5x3 dengan KREDIT VIRTUAL.
  Saldo awal 100.000 kredit, tombol "+ Tambah 100.000" = kredit demo.
  Tidak ada uang asli, deposit, atau penarikan.

Password default admin: SlotPedia2026 (ganti: edit ADMIN_SHA256/ADMIN_FNV di app.js lalu build).
Situs katalog informasi + simulasi demo - tanpa transaksi uang asli.
