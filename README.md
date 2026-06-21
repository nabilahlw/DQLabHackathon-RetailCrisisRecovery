# 🛒 DQLab Retail Crisis & Recovery — Hackathon Solution

**Challenge Code:** HACK-2026-PYTHON-01  
**Event:** DQLab × UjiKompetensi Python Hackathon (May 2026)

## 📌 Project Overview

DQFresh Mart experienced a sales decline over the last six months. However, some niche products were consistently growing but remained unnoticed by traditional sales reports.

This project builds an automated analytics pipeline to:

- Identify **Rising Star Products** using Moving Average trend analysis
- Discover **Product Bundling Opportunities** using the Apriori algorithm
- Generate business insights through data visualization and reporting

---

## 📂 Project Structure

```text
.
├── solusi-retail.py
├── sales_transaction.csv
├── retail_insight.xlsx
├── rising_star_index.png
└── rising_star_actual.png
```

Generated outputs:

- **retail_insight.xlsx** → Rising Star products & bundling recommendations
- **rising_star_index.png** → Relative growth chart (Base 100)
- **rising_star_actual.png** → Actual sales value chart

---

## 🚀 Installation

```bash
pip install pandas matplotlib mlxtend openpyxl
```

Run the script:

```bash
python solusi-retail.py
```

Make sure `sales_transaction.csv` is in the same directory.

---

## 📊 Dataset

The dataset contains 30 days of retail transaction data with the following fields:

| Column | Description |
|----------|-------------|
| nomor_struk | Transaction ID |
| tgl_transaksi | Transaction Date |
| kode_produk | Product Code |
| nama_produk | Product Name |
| jumlah_terjual | Quantity Sold |
| harga | Unit Price |
| total_nilai | Sales Value |

---

## 🔍 Methodology

### 1. Rising Star Detection

A 3-day Moving Average (MA) is used to smooth daily fluctuations.

Products are classified as **Rising Stars** if their Moving Average increases continuously for at least **12 consecutive days**.

Growth is calculated as:

```text
Growth % = ((Ending MA - Starting MA) / Starting MA) × 100
```

### 2. Product Bundling Analysis

Market Basket Analysis is performed using the **Apriori Algorithm**.

Parameters:

- Minimum Support: 1%
- Minimum Lift: 2
- At least one product in the rule must be a Rising Star

Association Rules are ranked by:

1. Lift
2. Support
3. Confidence

---

## 📈 Outputs

### Rising Star Products

Identifies products with strong and consistent growth trends.

### Potential Packaging

Recommends product combinations frequently purchased together, suitable for:

- Product bundling
- Cross-selling campaigns
- Store layout optimization

### Visualizations

- **Relative Growth Chart** (Base 100 normalization)
- **Actual Sales Value Chart**

These charts help compare growth performance and reveal promising products hidden behind low absolute sales volume.

---

## Business Impact

- Detect emerging products before they become top sellers
- Improve inventory planning
- Create effective bundling strategies
- Increase revenue through cross-selling opportunities

---

**Achievement:** Top 100 Finalist (#88 of 464 Participants) — DQLab Retail Crisis & Recovery Hackathon 2026 🚀
