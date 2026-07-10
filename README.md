# 📊 Digital Banking M&E Dashboard — Power BI
**Platform:** Power BI Desktop | PostgreSQL  
**Domain:** Monitoring & Evaluasi Digital Banking  
**Periode Data:** Juni 2024 (Simulasi)

---

## 📌 Latar Belakang

Dashboard ini merupakan visualisasi end-to-end dari pipeline monitoring transaksi 
digital banking — dari data yang tersimpan di **PostgreSQL** hingga menjadi laporan 
visual interaktif yang siap dipresentasikan kepada manajemen, Tim Teknis, 
dan Direksi Bank.

Dashboard dirancang mengikuti **identitas visual Bank Jatim** (dominasi merah 
#C0392B dan putih) dengan prinsip tata letak **golden ratio** untuk keterbacaan 
optimal.

---

## 🔧 Teknologi yang Digunakan

| Tools | Fungsi |
|-------|--------|
| Power BI Desktop | Dashboard development & visualisasi |
| PostgreSQL | Sumber data (direct connection) |
| DAX | Calculated measures & columns |
| Python (FastAPI) | Upstream data pipeline |

---

## 🏗️ Arsitektur Data Flow
PostgreSQL (db_me_digitalbanking)
↓
Power BI Direct Connection
↓
Power Query (Data Transformation)
↓
DAX Measures & Calculated Columns
↓
Interactive Dashboard (3 Pages)

---

## 📐 Struktur Dashboard

Dashboard terdiri dari **3 halaman** dengan pembaca dan tujuan berbeda:

### Page 1 — Operational Overview
> *Untuk: Manajemen & M&E — dibaca setiap pagi*

| Visual | Fungsi |
|--------|--------|
| 4 KPI Cards | Total transaksi, gagal, failed rate, status SLA |
| Line Chart | Tren transaksi harian dengan failed rate overlay |
| Multi-row Card | Status sistem real-time |
| Bar Chart | Rata-rata latency per channel |
| Summary Table | Ringkasan status dengan nilai rupiah |

**Pertanyaan yang dijawab:** *"Apakah sistem hari ini sehat?"*

---

### Page 2 — Channel & Failure Analysis
> *Untuk: Tim Teknis & Operasional*

| Visual | Fungsi |
|--------|--------|
| Bar Chart + SLA Line | Failed rate per channel vs threshold 5% |
| Donut Chart | Distribusi penyebab kegagalan |
| Eskalasi Table | Matriks PIC + jumlah kasus + nilai gagal |
| Heatmap Matrix | Channel vs failure reason dengan gradient merah |
| Slicer | Filter interaktif per channel |

**Pertanyaan yang dijawab:** *"Masalahnya di mana dan siapa yang bertindak?"*

---

### Page 3 — Branch Risk Monitor
> *Untuk: Direksi & Audit Internal*

| Visual | Fungsi |
|--------|--------|
| 4 KPI Cards | Total cabang, cabang breach, nilai at risk, max latency |
| Risk Ranking Table | Tabel cabang dengan conditional formatting risk level |
| Column Chart | Nilai transaksi gagal per cabang dengan color by risk |
| Treemap | Peta proporsi risiko seluruh cabang |

**Pertanyaan yang dijawab:** *"Cabang mana yang perlu perhatian segera?"*

---

## 📏 DAX Measures yang Digunakan

```dax
-- Measures Dasar
Total Transaksi    = COUNTROWS(transactions)
Total Gagal        = CALCULATE(COUNTROWS(transactions), transactions[status] = "failed")
Total Sukses       = CALCULATE(COUNTROWS(transactions), transactions[status] = "success")
Total Pending      = CALCULATE(COUNTROWS(transactions), transactions[status] = "pending")
Failed Rate        = DIVIDE([Total Gagal], [Total Transaksi], 0)
Total Nilai        = SUM(transactions[amount])
Nilai Gagal        = CALCULATE(SUM(transactions[amount]), transactions[status] = "failed")

-- Measures Monitoring
Status SLA         = IF([Failed Rate] > 0.05, "BREACH", "NORMAL")
Avg Latency MS     = AVERAGE(transactions[latency_ms])
Avg Latency Gagal  = CALCULATE(AVERAGE(transactions[latency_ms]), transactions[status] = "failed")
Total Cabang       = DISTINCTCOUNT(transactions[branch_code])
Cabang Breach      = COUNTROWS(FILTER(VALUES(transactions[branch_code]), CALCULATE([Failed Rate]) > 0.05))
Total Nilai At Risk= CALCULATE(SUM(transactions[amount]), transactions[status] = "failed")
Max Latency Cabang = MAXX(VALUES(transactions[branch_code]), CALCULATE(AVERAGE(transactions[latency_ms])))
```

---

## 🔍 Key Findings dari Dashboard

- **Status Sistem: BREACH** — failed rate 15.5% vs SLA 5%
- **Channel Terburuk:** EDC & Mobile Banking (failed rate >15%)
- **Penyebab Dominan:** Timeout & server error → eskalasi Tim Teknis
- **Cabang Berisiko Tertinggi:** Teridentifikasi lewat composite risk scoring

---

---

## 💡 Konsep Perbankan yang Diterapkan

- **Daily Health Check** — monitoring rutin pagi hari unit M&E
- **SLA Threshold Visualization** — garis referensi 5% di setiap chart
- **Risk Based Supervision** — pendekatan OJK dalam pengawasan
- **ALCO Reporting** — format laporan untuk komite aset-liabilitas
- **Escalation Matrix** — pemilahan Tim Teknis vs Operasional
- **Branch Performance Monitoring** — KPI cabang untuk Direksi

---

## 🎯 Relevansi dengan Posisi M&E Digital Banking

✅ Dashboard analytics (Power BI) sesuai kualifikasi khusus JD  
✅ Koneksi langsung ke database PostgreSQL — alur kerja realistis  
✅ Tiga perspektif pembaca: operasional, teknis, strategis  
✅ Conditional formatting & risk flagging untuk deteksi anomali  
✅ Desain mengikuti identitas visual Bank Jatim  

---

---

*Dibuat oleh Muhammad Fauzi Romadhoni — Portfolio M&E Digital Banking*
