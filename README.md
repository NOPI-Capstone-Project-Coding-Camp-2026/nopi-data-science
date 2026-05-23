# 🧾 NOPI (Nota Pintar)

> Pipeline Data Science untuk ekstraksi dan analisis informasi struk UMKM secara otomatis menggunakan OCR.

---

## 📌 Latar Belakang

Pelaku UMKM sering kali tidak memiliki sistem pencatatan transaksi yang terstruktur. NOPI hadir sebagai solusi yang memanfaatkan OCR untuk mengekstrak informasi dari foto struk secara otomatis, mulai dari nama barang, harga satuan, hingga estimasi laba.

---

## 📓 Notebooks

| Notebook | Deskripsi |
|---|---|
| `01_data_wrangling_image.ipynb` | Persiapan dataset gambar untuk training model CNN & pipeline OCR |
| `02_data_wrangling_csv.ipynb` | Wrangling, cleaning & EDA hasil OCR |

---

## ❓ Business Questions

| # | Pertanyaan |
|---|---|
| BQ1 | Bagaimana teknologi OCR dapat dimanfaatkan untuk mengekstrak informasi dari struk secara otomatis dan akurat? |
| BQ2 | Bagaimana pelaku usaha mikro dapat mengetahui estimasi laba dari setiap produk yang dijual secara sederhana dan efisien? |
| BQ3 | Bagaimana data transaksi hasil OCR dapat diolah menjadi laporan terstruktur untuk mendukung pengambilan keputusan bisnis? |

---

## 🗃️ Dataset

### Gambar

| Dataset | Deskripsi | Jumlah |
|---|---|---|
| Dataset CNN | Struk + non-struk untuk training CNN classifier | 2.117 gambar |
| Dataset OCR | Struk saja untuk pipeline OCR | 1.115 gambar |
| Data Primer | Foto struk asli hasil dokumentasi mandiri | 118 gambar |
| CORD-v2 | Dataset publik struk dari NAVER CLOVA AI | ~1.000 gambar |

> Dataset gambar tersedia untuk anggota tim. Hubungi tim Data Science NOPI untuk akses.  
> CORD-v2 tersedia secara publik di [HuggingFace](https://huggingface.co/datasets/naver-clova-ix/cord-v2).  
> Repository resmi CORD dari NAVER CLOVA AI tersedia di [GitHub](https://github.com/clovaai/cord).

### CSV (`data/raw/`)

| File | Deskripsi |
|---|---|
| `Dataset_Terstruktur_Primer_NOPI.csv` | Hasil parsing OCR dari raw output PaddleOCR |
| `evaluasi_3_model.csv` | Ringkasan performa 3 model OCR |
| `detail_akurasi_semua_model.csv` | Rincian akurasi per file per model |
| `hasil_komparasi_ocr_final.csv` | Data benchmark OCR |

---

## 📖 Data Dictionary

Lihat detail kolom dan tipe data: [data_dictionary.md](./data_dictionary.md)

---

## 📚 Referensi

- NAVER CLOVA AI. **CORD: Consolidated Receipt Dataset**. [GitHub](https://github.com/clovaai/cord)
- NAVER CLOVA AI. **CORD-v2 Dataset**. [HuggingFace](https://huggingface.co/datasets/naver-clova-ix/cord-v2)

---

## 👥 Tim

Dikembangkan oleh Tim Data Science NOPI Project.
