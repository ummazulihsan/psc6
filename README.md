# Monitor Sensus & Survei

Dashboard statis untuk memantau progres entri, kelengkapan dokumen, dan capaian target vs realisasi petugas sensus/survei. Data disimpan di Google Sheet, website di-host gratis di GitHub Pages.

## Struktur

- `index.html` — website dashboard (satu file, tidak perlu build tool)
- `Code.gs` — script backend yang dipasang di Google Sheet, menjadi API

## Langkah 1 — Siapkan Google Sheet

1. Buat Google Sheet baru.
2. Rename sheet pertama menjadi **`Data`** (persis, huruf besar D).
3. Isi baris pertama (header) dengan kolom berikut, urutannya bebas asal namanya sama persis:

   ```
   Kegiatan | Wilayah | Petugas | Target | Realisasi | Dokumen_Lengkap | Dokumen_Belum | Status | Update_Terakhir
   ```

4. Isi data di bawahnya, satu baris per petugas per kegiatan. Contoh:

   | Kegiatan | Wilayah | Petugas | Target | Realisasi | Dokumen_Lengkap | Dokumen_Belum | Status | Update_Terakhir |
   |---|---|---|---|---|---|---|---|---|
   | Sensus Penduduk 2026 | Kec. Pangkalpinang | Budi Santoso | 150 | 98 | 90 | 8 | Proses | 2026-08-10 |
   | Sensus Penduduk 2026 | Kec. Sungailiat | Sari Wulandari | 140 | 140 | 140 | 0 | Selesai | 2026-08-11 |

   Nilai `Status` yang dikenali dashboard: `Selesai`, `Proses`, `Terlambat` (case-insensitive).

## Langkah 2 — Pasang script backend (Apps Script)

1. Di Google Sheet, buka menu **Extensions > Apps Script**.
2. Hapus isi default `Code.gs`, lalu tempel isi file `Code.gs` dari folder ini.
3. Klik **Deploy > New deployment**.
4. Pilih tipe **Web app**.
5. Isi:
   - Execute as: **Me**
   - Who has access: **Anyone** (supaya website bisa mengambil data tanpa login)
6. Klik **Deploy**, lalu izinkan akses saat diminta (Authorize access).
7. Salin **URL Web App** yang muncul — bentuknya seperti:
   `https://script.google.com/macros/s/AKfycb.../exec`

   Simpan URL ini, akan dipakai di Langkah 4.

> Setiap kali kamu mengubah isi `Code.gs`, kamu perlu **Deploy > Manage deployments > edit (ikon pensil) > New version** supaya perubahan aktif.

## Langkah 3 — Publish website ke GitHub Pages

1. Buat repository baru di GitHub, misalnya `monitor-sensus`.
2. Upload file `index.html` ke repository tersebut (lewat web GitHub: **Add file > Upload files**, atau lewat `git push`).
3. Buka **Settings > Pages** di repository.
4. Di bagian **Source**, pilih branch `main` dan folder `/ (root)`, lalu **Save**.
5. Tunggu 1–2 menit, GitHub akan memberi URL seperti:
   `https://username.github.io/monitor-sensus/`

## Langkah 4 — Sambungkan website ke Google Sheet

1. Buka website yang sudah live tadi.
2. Klik tombol **Sambungkan data** di kanan atas.
3. Tempel URL Web App dari Langkah 2, lalu **Simpan & muat data**.
4. Dashboard akan langsung menampilkan data dari sheet kamu.

URL ini tersimpan di browser (localStorage) masing-masing pengguna, jadi setiap orang yang membuka dashboard perlu memasukkan URL yang sama sekali (cukup sekali per browser).

## Fitur

- **Ringkasan** — total target, realisasi, dokumen lengkap, dan persentase capaian keseluruhan.
- **Peta progres** — grid visual satu kotak per petugas, warna sesuai status, hover untuk detail.
- **Grafik capaian per wilayah** dan **status kelengkapan dokumen**.
- **Tabel petugas** dengan progres bar dan tombol **Update** untuk mengubah realisasi/kelengkapan dokumen langsung dari dashboard — perubahan tersimpan balik ke Google Sheet lewat Apps Script.
- Filter berdasarkan kegiatan (kalau ada lebih dari satu sensus/survei berjalan bersamaan).

## Catatan keamanan

Karena Web App diset "Anyone", siapa pun yang tahu URL-nya bisa membaca dan mengubah data. Untuk pemakaian internal ini biasanya cukup, tapi kalau butuh proteksi tambahan, opsi lanjutan:
- Tambahkan pengecekan token sederhana di `Code.gs` (cek parameter rahasia sebelum memproses).
- Batasi akses website via password sederhana di level GitHub Pages (butuh layanan tambahan seperti Cloudflare Access).
