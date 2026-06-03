# 🛒 DQLab Retail Crisis & Recovery — Hackathon Solution 

**Kode Soal:** `HACK-2026-PYTHON-01`
**Event:** Hackathon Python DQLab × UjiKompetensi Tanggal 9 Mei 2026
**Script:** `solusi-retail.py`

---

## 📌 Deskripsi Proyek

DQFresh Mart mengalami penurunan penjualan selama 6 bulan terakhir. Di balik kondisi ini, terdapat produk-produk kecil yang justru **tumbuh konsisten** namun tidak terdeteksi oleh laporan tradisional (top 10 produk). Proyek ini membangun pipeline analisis otomatis untuk:

1. Mendeteksi **Rising Star** — produk dengan tren kenaikan Moving Average ≥ 12 hari berturut-turut
2. Menemukan **Potential Packaging** — kombinasi produk yang sering dibeli bersamaan menggunakan algoritma Apriori
3. Menghasilkan **visualisasi** perbandingan pertumbuhan relatif dan nilai aktual

---

## 📁 Struktur File

```
.
├── solusi-retail.py          # Script utama (jawaban, yg perlu dijalankan)
├── sales_transaction.csv     # Dataset input (30 hari transaksi)
├── retail_insight.xlsx       # OUTPUT: Excel dengan 2 sheet
├── rising_star_index.png     # OUTPUT: Chart pertumbuhan relatif (base 100)
└── rising_star_actual.png    # OUTPUT: Chart nilai penjualan aktual
```

> ⚠️ 3 File output di-generate otomatis saat script dijalankan. 

---

## 🔧 Requirements

| Library    | Versi   | Fungsi                                      |
|------------|---------|---------------------------------------------|
| Python     | 3.10–3.14 | Runtime                                   |
| `pandas`   | 2.3.1   | Manipulasi data, rolling window, groupby    |
| `matplotlib` | 3.10.7 | Visualisasi line chart                    |
| `mlxtend`  | 0.23.4  | Algoritma Apriori & Association Rules       |
| `openpyxl` | 3.1.5   | Export Excel + formatting (bold, autowidth) |

---

## 🚀 Cara Menjalankan
### Instalasi

```bash
pip install pandas==2.3.1 matplotlib==3.10.7 mlxtend==0.23.4 openpyxl==3.1.5
```

Pastikan `sales_transaction.csv` berada di folder yang sama dengan `solusi-retail.py`, lalu jalankan:

```bash
python solusi-retail.py
```

Script akan menghasilkan 3 file output langsung di folder tersebut.

---

## 📊 Sumber Data: tabel `sales_transaction.xlsx`

| Kolom          | Tipe    | Keterangan                              |
|----------------|---------|-----------------------------------------|
| `nomor_struk`  | string  | Nomor invoice / struk transaksi         |
| `tgl_transaksi`| date    | Tanggal transaksi (format: YYYY-MM-DD)  |
| `kode_produk`  | string  | Kode unik SKU produk                    |
| `nama_produk`  | string  | Nama produk                             |
| `jumlah_terjual` | int   | Quantity produk yang terjual            |
| `harga`        | float   | Harga satuan produk                     |
| `total_nilai`  | float   | `harga × jumlah_terjual`                |

- Periode: **30 hari** transaksi
- Tidak ada data yang perlu dicleansing

---

## 🧮 Metodologi & Rumus

### 1. Moving Average (MA) — Smoothing

Digunakan untuk mengurangi fluktuasi harian yang ekstrem.

```
MA(t) = rata-rata total_nilai selama 3 hari terakhir (window=3)
```

### 2. Deteksi Tren Naik (Rising Trend)

Sebuah produk masuk **sesi tren naik** jika:

```
MA(hari ini) > MA(hari sebelumnya)
```

Lalu dihitung berapa hari kenaikan tersebut terjadi **secara berurutan** (consecutive days) per sesi tren.

### 3. Filter Rising Star

Produk hanya masuk Rising Star jika pernah mengalami:

```
Consecutive Rising Days ≥ 12 hari
```

### 4. Growth % (Metode Endpoint vs Startpoint)

Pertumbuhan dihitung dari titik awal ke titik akhir **sesi tren naik** terpanjang:

```
Growth % = ((MA_akhir - MA_awal) / MA_awal) × 100
```

Diambil nilai maksimum Growth % per produk (dari seluruh sesi tren yang ada).

### 5. Normalisasi Base 100 (untuk Chart Index)

Agar semua produk bisa dibandingkan secara adil (terlepas dari skala nilai absolutnya):

```
Normalized(t) = (MA(t) / MA_pertama) × 100
```

Semua produk dimulai dari titik 100 pada hari pertama pengamatan.

### 6. Algoritma Apriori — Frequent Itemset Mining

Membentuk basket matrix: satu baris per struk, satu kolom per produk (nilai binary 0/1).

```python
basket = (raw.groupby(['nomor_struk', 'nama_produk'])['jumlah_terjual']
    .sum().unstack(fill_value=0) > 0).astype(int)

freq_items = apriori(basket, min_support=0.01, use_colnames=True)
```

Parameter:
- `min_support = 0.01` → minimal 1% dari total transaksi

### 7. Association Rules

```python
rules = association_rules(freq_items, metric='lift', min_threshold=1)
```

**Filter akhir:**
- Minimal **satu item** dalam rule (antecedent atau consequent) harus merupakan produk Rising Star
- `lift ≥ 2`
- Diurutkan: **Lift → Support → Confidence** (descending)

**Interpretasi metrik:**

| Metrik     | Rumus                                      | Arti                                              |
|------------|--------------------------------------------|---------------------------------------------------|
| Support    | P(A ∩ B)                                   | Seberapa sering kombinasi A+B muncul              |
| Confidence | P(B\|A) = P(A ∩ B) / P(A)                 | Jika beli A, seberapa sering juga beli B          |
| Lift       | Confidence / P(B)                          | Apakah A dan B benar-benar saling mempengaruhi    |

> Lift > 1 = positif, Lift ≥ 2 = asosiasi kuat (dipakai di proyek ini)

---

## 📤 Output

### 1. `retail_insight.xlsx`

| Sheet               | Kolom                                                      |
|---------------------|------------------------------------------------------------|
| **Rising Star**     | Kode Produk, Nama Produk, Growth %, Total Penjualan        |
| **Potential Packaging** | Jika Membeli, Maka Membeli, Jumlah Invoice, Support, Confidence, Lift |

### 2. `rising_star_index.png` — Pertumbuhan Relatif

- Line chart semua Rising Star dinormalisasi base 100
- Dibandingkan dengan Top 3 produk berdasarkan total penjualan (garis abu-abu putus)
- Tujuan: membandingkan **kecepatan tumbuh** secara adil tanpa pengaruh skala nilai

### 3. `rising_star_actual.png` — Nilai Penjualan Aktual

- Line chart Rising Star dengan nilai `total_nilai` asli (tanpa normalisasi)
- Memvisualisasikan **mengapa produk ini tidak terdeteksi** — nilainya kecil dibanding Top 3

---

## 💡 Insight Bisnis

| Temuan              | Rekomendasi                                              |
|---------------------|----------------------------------------------------------|
| Rising Star teridentifikasi | Segera tambah stok produk tersebut              |
| Potential Packaging | Buat paket bundling, promo cross-sell, atur display toko |
| Lift tinggi         | Produk A dan B saling "menarik" — efektif dijual bersama |

---

*Huge Thanks for DQLab × UjiKompetensi Hackathon 2026*
