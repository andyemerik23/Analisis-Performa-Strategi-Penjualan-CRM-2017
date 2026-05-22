# Analisis Performa & Strategi Penjualan CRM (2017)

---

## 📑 Validitas & Sumber Data (Data Provenance)

Untuk menjaga akuntabilitas, transparansi, dan reproduksibilitas data pada kompetisi Satria Data Big Data Challenge (BDC), proyek ini menggunakan dataset publik resmi dengan detail metadata sebagai berikut:

* **Nama Dataset:** CRM Sales Opportunities
* **Sumber Platform:** [Maven Analytics Data Playground](https://app.mavenanalytics.io/datasets?search=crm+sales+opportunities) *(Original source: data.world)*
* **Format File:** CSV (Multiple Tables)
* **Struktur Data:** Basis data relasional yang terdiri dari tabel `sales_pipeline`, `accounts`, `products`, dan `sales_teams`.
* **Jumlah Baris (Records):** 8.800 baris data transaksi pipa penjualan B2B.
* **Jumlah Kolom (Fields):** 18 fitur yang mencakup dimensi waktu (*time series*), metrik finansial, serta data operasional tim sales dan profil perusahaan klien.
* **Konteks Data:** Data pipa penjualan (*sales pipeline*) B2B dari perusahaan fiktif yang menjual perangkat keras komputer (*computer hardware*).
* **Tanggal Rilis Data:** 04 Maret 2024

---
   
## 📋 Gambaran Umum Proyek

Proyek ini berfokus pada analisis jalur penjualan (*sales pipeline*) B2B untuk mengungkap tren performa regional, penetrasi produk, dan pertumbuhan pendapatan. Dengan memanfaatkan teknik SQL tingkat lanjut, data CRM mentah ditransformasikan menjadi wawasan strategis yang dapat ditindaklanjuti terkait kecepatan penjualan (*sales velocity*), produktivitas agen, dan segmentasi pasar.

Tujuan utama dari analisis ini adalah untuk menjawab pertanyaan bisnis krusial berikut:
* Wilayah dan agen mana yang menghasilkan pendapatan tertinggi?
* Seberapa cepat suatu transaksi (*deals*) bergerak melalui jalur penjualan kami?
* Bagaimana trajektori pendapatan perusahaan berdasarkan *Year-to-Date* (YTD) dan *Month-over-Month* (MoM)?
* Produk apa saja yang mendominasi sektor Medis (*Medical*) dan Teknologi (*Technology*)?

---

## 🛠️ Kemampuan Teknis yang Didemonstrasikan

* **Agregasi Data & Views:** Membuat objek `VIEW` yang dapat digunakan kembali untuk pelaporan modular.
* **Window Functions:** Memanfaatkan fungsi `DENSE_RANK()`, `SUM() OVER()`, dan `LAG()` untuk akumulasi total berjalan (*running total*), pemeringkatan, dan analisis tren pertumbuhan.
* **Common Table Expressions (CTEs):** Menyusun kalkulasi kompleks yang melibatkan banyak tahapan agar kode lebih bersih dan mudah dibaca.
* **Logika Kondisional:** Menggunakan perintah `CASE WHEN`, `COALESCE`, dan `NULLIF` untuk pembersihan data (*data cleaning*) dan pengelompokan variabel (*bucketing*).
* **Operasi Join:** Menerapkan `INNER JOIN` dan `LEFT JOIN` untuk mengintegrasikan data penjualan, data akun klien, dan data dimensi produk.
* **Pencocokan Pola (Pattern Matching):** Menggunakan klausa `LIKE` dengan *wildcards* dan fungsi `LOWER()` untuk analisis sektor industri yang sensitif terhadap huruf besar/kecil (*case-insensitive*).

---

## 🔍 Analisis Utama & Wawasan Bisnis

### 1. Dinamika Pendapatan (YTD & MoM)
* **Total Berjalan Harian (Daily Running Total):** Analisis deret waktu (*time-series*) yang menunjukkan akumulasi pertumbuhan pendapatan sepanjang tahun. Menggunakan pengurut unik (*tie-breakers*) untuk memastikan akumulasi baris demi baris berjalan akurat.
* **Pertumbuhan Bulan-ke-Bulan (Month-over-Month/MoM Growth):** Menghitung persentase perubahan pendapatan bulanan untuk mengidentifikasi puncak musiman (*seasonal peaks*) dan penurunan performa.

### 2. Performa Regional & Agen Sales
* **Top 3 Agen per Wilayah:** Menggunakan fungsi `DENSE_RANK()` yang dipartisi berdasarkan wilayah untuk mengidentifikasi staf dengan performa penjualan tertinggi di setiap kantor regional.
* **Visualisasi Penjualan Regional:** Mengonsolidasikan data peluang penjualan dengan lokasi tim sales untuk memetakan distribusi geografis pendapatan.

### 3. Kecepatan Penjualan & Sentimen Pasar
* **Kelompok Kecepatan Transaksi (Deal Velocity Buckets):** Mengklasifikasikan transaksi ke dalam kategori Cepat (<30 hari), Sedang, dan Lambat (>90 hari) berdasarkan durasi waktu dari tahap awal interaksi (*engagement*) hingga transaksi ditutup (*closing*).
* **Analisis Mendalam Sektor Industri:** Membedah sektor "Medis" dan "Teknologi" untuk mengidentifikasi 3 produk teratas berdasarkan volume transaksi di masing-masing industri.

### 4. Kesehatan Finansial & Hierarki Perusahaan
* **Analisis Nilai Jual:** Mengidentifikasi transaksi dengan margin tinggi, di mana nilai penutupan (*closing value*) secara signifikan melebihi harga jual standar produk.
* **Hierarki Akun Perusahaan:** Membersihkan data relasi induk-anak (*parent-subsidiary*) menggunakan fungsi `COALESCE` untuk membedakan secara jelas antara anak perusahaan korporat dan akun independen.

---

## 📊 Arsitektur Database & EER Diagram

Proyek ini menggunakan basis data relasional untuk mengelola data operasional CRM secara terintegrasi. Berikut adalah cetak biru skema database (*Enhanced Entity-Relationship Diagram*) yang diekstrak langsung menggunakan MySQL Workbench:

![CRM Sales Opportunities EER Diagram](Database%20Architecture%20&%20EER%20Diagram.png)

---

## 📖 Kamus Data (Data Dictionary)

Dokumentasi meta-data dari tabel-tabel utama yang telah diintegrasikan ke dalam sistem basis data:

### 1. Tabel `sales_pipeline` (Fakta Transaksi)
* `opportunity_id` (VARCHAR) : ID unik untuk setiap peluang penjualan (Primary Key).
* `sales_agent` (VARCHAR) : Nama agen penjualan yang menangani prospek (Foreign Key).
* `product` (VARCHAR) : Nama produk hardware yang ditawarkan (Foreign Key).
* `account` (VARCHAR) : Nama perusahaan klien / B2B (Foreign Key).
* `deal_stage` (TEXT) : Tahapan transaksi saat ini (`Prospecting`, `Engaged`, `Won`, `Lost`).
* `engage_date` (DATE) : Tanggal dimulainya interaksi aktif dengan klien.
* `close_date` (DATE) : Tanggal saat transaksi resmi selesai ditutup.
* `close_value` (INT) : Nilai pendapatan yang dihasilkan jika transaksi berhasil (*Won*).

### 2. Tabel `accounts` (Dimensi Perusahaan Klien)
* `account` (VARCHAR) : Nama perusahaan klien (Primary Key).
* `sector` (TEXT) : Sektor industri klien (misal: *Technology, Finance, Retail*).
* `year_established` (INT) : Tahun berdirinya perusahaan klien.
* `revenue` (DOUBLE) : Estimasi pendapatan tahunan perusahaan klien.
* `employees` (INT) : Jumlah karyawan perusahaan klien.
* `office_location` (TEXT) : Lokasi kantor utama klien.

### 3. Tabel `products` (Dimensi Produk)
* `product` (VARCHAR) : Nama produk hardware (Primary Key).
* `series` (TEXT) : Seri atau lini produk.
* `sales_price` (INT) : Harga jual standar produk.

### 4. Tabel `sales_teams` (Dimensi Agen Sales)
* `sales_agent` (VARCHAR) : Nama agen penjualan (Primary Key).
* `manager` (TEXT) : Nama manajer yang mengepalai agen tersebut.
* `regional_office` (TEXT) : Wilayah operasional kantor agen.

---

## 📂 Struktur Repositori

```text
├── Analysis.sql                                # Skrip query SQL utama untuk metrik analitik lanjutan
├── Database Architecture & EER Diagram.png     # Cetak biru visual skema basis data
├── README.md                                   # Dokumentasi lengkap proyek dan meta-data
└── tables_setup.sql                            # Skrip DDL untuk pembuatan tabel dan konstrain database
