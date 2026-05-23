# 📖 Data Dictionary — NOPI

---

## Notebook 01 — Dataset Gambar (`manifest.csv`)

Dataset NOPI terdiri dari gambar struk dan non-struk yang telah melalui proses cleaning dan siap digunakan untuk training model CNN dan pipeline OCR.

| Kolom | Tipe | Deskripsi | Contoh |
|---|---|---|---|
| `id` | string | ID unik gambar | `train_0001` |
| `label` | string | Kelas gambar — **target variabel** | `struk` / `non_struk` |
| `split` | string | Partisi dataset | `train` / `val` / `test` |
| `source` | string | Asal data | `cord-v2` / `gdrive` / `primer` |
| `width` | int | Lebar gambar dalam piksel | `864` |
| `height` | int | Tinggi gambar dalam piksel | `1296` |
| `md5` | string | Hash MD5 file untuk deteksi duplikat | `577d87ca...` |
| `raw_path` | string | Path file gambar original | `/content/dataset/raw/...` |
| `cnn_path` | string | Path file gambar untuk training CNN | `/content/dataset/cnn/...` |
| `ocr_path` | string | Path file untuk pipeline OCR — hanya terisi untuk kelas `struk` | `/content/dataset/ocr/...` |
| `aspect_ratio` | float | Rasio lebar dibagi tinggi (`width/height`) | `0.67` |
| `orientation` | bool | Orientasi gambar (`True` = portrait) | `True` |
| `resolution` | int | Total piksel (`width x height`) | `1,120,000` |
| `res_category` | category | Kategori resolusi: `low` <100K, `mid` <500K, `high` >=500K px | `high` |
| `label_encoded` | int | Label numerik: `struk=1`, `non_struk=0` — **target, bukan fitur** | `1` |
| `source_encoded` | int | Source numerik: `cord-v2=0`, `gdrive=1`, `primer=2` | `0` |
| `ar_category` | category | Kategori aspect ratio: `portrait` <0.8, `square` 0.8–1.2, `landscape` >1.2 | `portrait` |

### Catatan
- `label` dan `label_encoded` adalah target variabel — tidak boleh digunakan sebagai fitur training
- `ocr_path` hanya terisi untuk baris dengan `label == 'struk'`, sisanya `NaN`
- `md5` digunakan sebagai primary key untuk deteksi duplikat dan cross-leakage check
- `split` dibagi dengan rasio **80/10/10** (train/val/test) per label secara stratified

---

## Notebook 02 — Dataset OCR (`df_clean_primer.csv`)

**Model OCR:** PaddleOCR — dipilih berdasarkan evaluasi performa (Success Rate 73.3%)  
**Total data bersih:** 192 baris  
**Total struk:** 53 file  

| Kolom | Tipe | Deskripsi |
|---|---|---|
| `filename` | str | Nama file foto struk yang diproses OCR |
| `sumber` | str | Sumber data transaksi |
| `nama_toko` | str | Nama toko hasil ekstraksi OCR |
| `tanggal_clean` | datetime | Tanggal transaksi setelah standardisasi |
| `tanggal_valid` | bool | Penanda apakah tanggal berhasil distandardisasi |
| `nama_barang` | str | Nama item hasil OCR yang sudah dibersihkan |
| `jumlah_barang` | float | Jumlah unit item pada baris transaksi |
| `harga_satuan` | float | Harga per unit dalam Rupiah setelah validasi dan pembatasan outlier |
| `total_harga_item` | float | Total harga item hasil OCR atau hasil rekalkulasi jika nilai awal kosong |
| `kategori_harga` | str | Segmentasi item berdasarkan rentang harga satuan |
| `bulan` | float | Bulan transaksi dari `tanggal_clean` |
| `tahun` | float | Tahun transaksi dari `tanggal_clean` |
| `bulan_tahun` | str | Periode transaksi dalam format YYYY-MM |
| `estimasi_laba_20` | float | Estimasi laba berdasarkan asumsi margin 20% dari `total_harga_item` |

### Catatan Cleaning
- Tanggal tidak valid ditandai dengan `tanggal_valid=False` dan tidak dihapus
- Harga satuan ekstrem dibatasi menggunakan persentil ke-99
- Baris dengan jumlah barang > 200 unit dihapus sebagai noise OCR
- Baris dengan inkonsistensi harga × qty vs total > 50% dihapus
- Nama barang dibersihkan dari karakter noise OCR dan dinormalisasi
- Estimasi laba menggunakan asumsi margin 20%, bukan laba aktual
