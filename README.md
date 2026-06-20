# gas-sensor-pca-breath-analysis-TB

# Eksplorasi Pola Respons Sensor Gas MOS terhadap Senyawa VOC Menggunakan PCA sebagai Pendekatan *Breath Analysis* Tuberkulosis Paru

Eksplorasi pola distribusi respons 16 sensor gas Metal Oxide Semiconductor (MOS) terhadap 6 senyawa Volatile Organic Compounds (VOC) menggunakan Principal Component Analysis (PCA), sebagai kajian awal pendekatan *breath analysis* non-invasif untuk skrining tuberkulosis paru.

## Latar Belakang

Tuberkulosis (TBC) paru masih menjadi masalah kesehatan global, dengan Indonesia menempati urutan kedua dunia (estimasi 1.060.000 kasus, 2023). Pendekatan *breath analysis* berbasis VOC berkembang sebagai alternatif skrining non-invasif, namun implementasinya masih bergantung pada instrumen Gas Chromatography-Mass Spectrometry (GC-MS) yang mahal dan kurang aplikatif untuk fasilitas kesehatan primer.

Proyek ini bersifat **eksploratif**: mengkaji apakah pola respons sensor gas MOS (yang jauh lebih murah dan portabel dibanding GC-MS) terhadap berbagai VOC dapat dibedakan secara visual menggunakan PCA, serta menganalisis relevansinya dengan biomarker VOC tuberkulosis berdasarkan kajian literatur.

## Dataset

**Gas Sensor Array Drift Dataset at Different Concentrations**
Sumber: [UCI Machine Learning Repository](https://archive.ics.uci.edu/dataset/270/gas+sensor+array+drift+dataset+at+different+concentrations) (Vergara, 2012; Fonollosa et al., 2015)

- 13.910 data pengukuran respons sensor gas
- 16 sensor MOS (seri TGS2600, TGS2602, TGS2610, TGS2620 — Figaro Engineering)
- 6 senyawa VOC pada berbagai tingkat konsentrasi
- Dikumpulkan selama 36 bulan (Januari 2008 – Februari 2011), dibagi menjadi 10 batch untuk analisis fenomena *sensor drift*

| Label | Jenis Gas | Rentang Konsentrasi (ppmv) |
|---|---|---|
| 1 | Ethanol | 10 – 600 |
| 2 | Ethylene | 10 – 300 |
| 3 | Ammonia | 50 – 1000 |
| 4 | Acetaldehyde | 5 – 500 |
| 5 | Acetone | 12 – 1000 |
| 6 | Toluene | 10 – 100 |

## Struktur Repositori

```
.
├── notebooks/
│   ├── 01_preprocessing.ipynb       # Parsing .dat -> .csv, ekstraksi 128 fitur
│   └── 02_pca_visualisasi.ipynb     # Normalisasi, PCA 2D, visualisasi & explained variance
├── data/
│   └── batch1_clean.csv ... batch10_clean.csv       # Hasil preprocessing
├── results/
│   ├── pca_batch1.png ... pca_batch10.png           # Visualisasi PCA per batch
│   └── pca_variance_summary.csv                     # Ringkasan explained variance tiap batch
└── README.md
```

## Alur Kerja

1. **Unduh dataset mentah** dari UCI ML Repository (`batch1.dat` s/d `batch10.dat`)
2. **Preprocessing** (`01_preprocessing.ipynb`) — parsing format LIBSVM-style, ekstraksi label, concentration level, dan 128 feature vector (16 sensor × 8 fitur: ΔR, |ΔR|, EMA increasing/decreasing pada α = 0.001, 0.01, 0.1) → output disimpan ke `data/batch{n}_clean.csv`
3. **Normalisasi & Reduksi Dimensi** (`02_pca_visualisasi.ipynb`) — `StandardScaler` → `PCA(n_components=2)` → visualisasi scatter plot per batch (warna = jenis gas, ukuran titik = konsentrasi). Notebook ini juga menghitung *explained variance ratio* PC1 dan PC2 serta jumlah komponen yang dibutuhkan untuk menangkap 95% variansi data, lalu menyimpan ringkasannya ke `results/pca_variance_summary.csv`
4. **Analisis** — pola distribusi dan separability antarklaster VOC diinterpretasikan menggunakan konsep *cross-sensitivity* sensor MOS dan dikaitkan dengan literatur biomarker VOC tuberkulosis

> Catatan: proyek ini berfokus pada eksplorasi pola (unsupervised) menggunakan PCA, tanpa tahap klasifikasi *supervised* (mis. SVM).

## Cara Menjalankan

```bash
git clone git@github.com:adeliashahahahaha/gas-sensor-pca-breath-analysis-TB.git
cd gas-sensor-pca-breath-analysis-TB
```

1. Unduh dataset dari [UCI ML Repository](https://archive.ics.uci.edu/dataset/270/gas+sensor+array+drift+dataset+at+different+concentrations), ekstrak ZIP-nya, taruh `batch1.dat` s/d `batch10.dat` di root folder
2. Jalankan `notebooks/01_preprocessing.ipynb` → menghasilkan 10 file `.csv` di folder `data/`
3. Jalankan `notebooks/02_pca_visualisasi.ipynb` → menghasilkan visualisasi PCA per batch dan tabel ringkasan explained variance di folder `results/`

### Dependensi
```
pandas
numpy
scikit-learn
matplotlib
seaborn
```

## Referensi Dataset

Fonollosa, J., Rodríguez-Luján, I., & Huerta, R. (2015). Chemical gas sensor array dataset. *Data in Brief*, 3, 85–89. https://doi.org/10.1016/j.dib.2015.01.003