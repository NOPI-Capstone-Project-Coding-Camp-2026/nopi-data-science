# Data Dictionary NOPI

---

## Notebook 01 Dataset Gambar

Dataset NOPI terdiri dari gambar struk dan non-struk yang telah melalui proses cleaning dan siap digunakan untuk training model CNN sebagai filter input sebelum pipeline OCR.

| Kolom | Tipe | Deskripsi | Contoh |
|---|---|---|---|
| `id` | string | ID unik gambar | `train_0001` |
| `label` | string | Kelas gambar dan target variabel utama | `struk` / `non_struk` |
| `split` | string | Partisi dataset untuk training, validation, dan testing | `train` / `val` / `test` |
| `source` | string | Asal data gambar | `cord-v2` / `gdrive` / `primer` |
| `width` | int | Lebar gambar dalam piksel | `864` |
| `height` | int | Tinggi gambar dalam piksel | `1296` |
| `md5` | string | Hash MD5 file untuk deteksi duplikat dan pengecekan data leakage | `577d87ca...` |
| `raw_path` | string | Path file gambar original sebelum preprocessing | `/content/dataset/raw/...` |
| `cnn_path` | string | Path file gambar hasil preprocessing untuk training CNN | `/content/dataset/cnn/...` |
| `ocr_path` | string | Path file untuk pipeline OCR, hanya terisi untuk kelas `struk` | `/content/dataset/ocr/...` |
| `aspect_ratio` | float | Rasio lebar dibagi tinggi (`width/height`) | `0.67` |
| `orientation` | bool | Orientasi gambar, dengan `True` berarti portrait | `True` |
| `resolution` | int | Total piksel gambar (`width x height`) | `1,120,000` |
| `res_category` | category | Kategori resolusi: `low` < 100K, `mid` < 500K, `high` >= 500K piksel | `high` |
| `label_encoded` | int | Label numerik untuk target model: `struk=1`, `non_struk=0` | `1` |
| `source_encoded` | int | Source numerik untuk kebutuhan metadata analisis: `cord-v2=0`, `gdrive=1`, `primer=2` | `0` |
| `ar_category` | category | Kategori aspect ratio: `portrait` < 0.8, `square` 0.8–1.2, `landscape` > 1.2 | `portrait` |

### Catatan

- `label` adalah **target variabel** dan tidak boleh digunakan sebagai fitur input training.
- `label_encoded` adalah versi numerik dari `label`, sehingga juga berperan sebagai target, bukan fitur input.
- `source_encoded` hanya digunakan sebagai metadata analisis, bukan fitur training CNN, karena sumber data dapat berkorelasi kuat dengan label.
- `ocr_path` hanya terisi untuk baris dengan `label == 'struk'`, sedangkan baris `non_struk` bernilai `NaN`.
- `md5` digunakan sebagai identifier untuk deteksi duplikat dan cross-leakage check.
- `split` dibagi dengan rasio **80/10/10** (train/val/test) per label secara stratified.
- Dataset gambar digunakan untuk mendukung tahap klasifikasi CNN sebagai filter sebelum proses OCR.

---

## Notebook 02 Dataset OCR (`df_clean_primer.csv`)

Dataset OCR merupakan data transaksi hasil ekstraksi PaddleOCR yang telah melalui proses cleaning, validasi, feature engineering, dan siap digunakan untuk analisis transaksi.

**Model OCR:** PaddleOCR, dipilih berdasarkan evaluasi performa dengan success rate **73.33%**  
**Total data bersih:** 192 baris  
**Total struk:** 53 file  

| Kolom | Tipe | Deskripsi | Contoh |
|---|---|---|---|
| `filename` | str | Nama file foto struk yang diproses OCR | `primer_0079.jpg` |
| `sumber` | str | Sumber data transaksi | `primer` |
| `nama_toko` | str | Nama toko hasil ekstraksi OCR | `Tokosukatam` |
| `tanggal_clean` | datetime | Tanggal transaksi setelah standardisasi | `2026-01-24` |
| `tanggal_valid` | bool | Penanda apakah tanggal berhasil distandardisasi | `True` / `False` |
| `nama_barang` | str | Nama item hasil OCR yang sudah dibersihkan | `Kanzlr Bakso Ori 48G` |
| `jumlah_barang` | float | Jumlah unit item pada baris transaksi | `2.0` |
| `harga_satuan` | float | Harga per unit dalam Rupiah setelah validasi dan pembatasan outlier | `17400.0` |
| `total_harga_item` | float | Total harga item hasil OCR atau hasil rekalkulasi jika nilai awal 0/tidak valid | `34800.0` |
| `kategori_harga` | str | Segmentasi item berdasarkan rentang harga satuan | `Murah (5-20rb)` |
| `bulan` | float | Bulan transaksi dari `tanggal_clean` | `1.0` |
| `tahun` | float | Tahun transaksi dari `tanggal_clean` | `2026.0` |
| `bulan_tahun` | str | Periode transaksi dalam format YYYY-MM | `2026-01` |
| `estimasi_laba_20` | float | Estimasi laba berdasarkan asumsi margin 20% dari `total_harga_item` | `6960.0` |

### Catatan Cleaning

- Dataset final merupakan hasil cleaning dari output OCR, sehingga beberapa nilai masih mungkin mengandung noise residual.
- Kolom transaksi utama seperti `nama_barang`, `jumlah_barang`, `harga_satuan`, dan `total_harga_item` sudah dibersihkan dari missing value.
- Tanggal tidak valid ditandai dengan `tanggal_valid=False` dan tidak dihapus, sehingga `tanggal_clean`, `bulan`, `tahun`, dan `bulan_tahun` dapat bernilai kosong pada baris tertentu.
- Harga satuan ekstrem dibatasi menggunakan persentil ke-99 untuk mengurangi pengaruh outlier OCR.
- Baris dengan `jumlah_barang > 200` dihapus karena dianggap sebagai noise OCR ekstrem.
- Baris dengan inkonsistensi antara `harga_satuan × jumlah_barang` dan `total_harga_item` lebih dari 50% dihapus.
- Nama barang dibersihkan dari karakter noise OCR dan dinormalisasi agar lebih mudah dianalisis.
- `estimasi_laba_20` merupakan estimasi sederhana berbasis margin 20%, bukan laba aktual karena dataset belum memiliki informasi harga beli atau HPP.
- Analisis total transaksi perlu memperhatikan outlier karena beberapa nilai total harga masih berpotensi besar akibat noise OCR.
