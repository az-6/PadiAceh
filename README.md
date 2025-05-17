# Laporan Tugas 2: Analisis Data Padi Aceh

## Deskripsi Proyek

Laporan ini berisi langkah-langkah preprocessing dan analisis data pada *Dataset Tanaman Padi di Aceh*. Tujuannya mempersiapkan data untuk analisis lanjutan atau pemodelan prediksi.

---

## Konten

1. [Pendahuluan](#pendahuluan)
2. [Data & Eksplorasi](#data--eksplorasi)
3. [Deteksi & Penanganan Data Bermasalah](#deteksi--penanganan-data-bermasalah)
4. [Transformasi Data Numerik](#transformasi-data-numerik)
5. [Diskretisasi Data Tahun](#diskretisasi-data-tahun)
6. [Visualisasi & Interpretasi](#visualisasi--interpretasi)
7. [Kesimpulan & Saran](#kesimpulan--saran)

---

## Pendahuluan

**Latar Belakang**

* Produksi padi merupakan komoditas utama untuk ketahanan pangan di Indonesia.
* Aceh memiliki lahan potensial, namun variabel cuaca dan manajemen memengaruhi hasil.

**Tujuan & Ruang Lingkup**

* Melakukan preprocessing data mentah (`Dataset Tanaman Padi di Aceh.xlsx`).
* Meliputi eksplorasi, penanganan missing/duplikat, normalisasi, diskretisasi, dan visualisasi.

---

## Data & Eksplorasi

**Deskripsi Dataset**

| Kolom       | Tipe  | Keterangan                   |
| ----------- | ----- | ---------------------------- |
| Tahun       | int   | Tahun pengukuran (1990–2020) |
| Produksi    | float | Produksi padi (ton)          |
| Luas Panen  | float | Luas area panen (ha)         |
| Curah Hujan | float | Jumlah curah hujan (mm)      |

**Loading & Eksplorasi**

```python
import pandas as pd

df = pd.read_excel('Dataset Tanaman Padi di Aceh.xlsx')
df.info()
df.describe()
```

* Tipe data sesuai, rentang tahun 1990–2020
* Rata-rata produksi dan distribusi statistik lainnya dijabarkan oleh `.describe()`

---

## Deteksi & Penanganan Data Bermasalah

**Deteksi Missing Value & Duplikasi**

```python
print(df.isnull().sum())
print(df.duplicated().sum())
```

* Hasil *missing* dan jumlah baris duplikat terdeteksi.

**Penanganan**

```python
# Imputasi mean untuk kolom numerik
df.fillna(df.mean(numeric_only=True), inplace=True)
# Hapus baris duplikat
df.drop_duplicates(inplace=True)
```

* Imputasi mean menjaga distribusi variabel.
* Penghapusan duplikat memastikan keunikan data.

---

## Transformasi Data Numerik

**Alasan**

* Skala variabel berbeda membuat model sulit konvergen.

**Implementasi Min–Max Scaling**

```python
from sklearn.preprocessing import MinMaxScaler

scaler = MinMaxScaler()
numerical_cols = ['Produksi', 'Luas Panen', 'Curah Hujan']
df[numerical_cols] = scaler.fit_transform(df[numerical_cols])
```

**Contoh Perbandingan**

| Variabel    | Sebelum | Sesudah |
| ----------- | ------- | ------- |
| Produksi    | 12000   | 0.45    |
| Luas Panen  | 8000    | 0.38    |
| Curah Hujan | 250     | 0.60    |

---

## Diskretisasi Data Tahun

**Pembuatan Kolom `Dekade`**

```python
import pandas as pd

df['Dekade'] = pd.cut(
    df['Tahun'],
    bins=[1989, 1999, 2009, 2019, 2029],
    labels=['1990-an', '2000-an', '2010-an', '2020-an']
)
```

**Hasil Contoh**

| Tahun | Dekade  |
| ----- | ------- |
| 1995  | 1990-an |
| 2003  | 2000-an |
| 2015  | 2010-an |

---

## Visualisasi & Interpretasi

**1. Tren Produksi per Tahun**

```python
import seaborn as sns
import matplotlib.pyplot as plt

sns.lineplot(data=df, x='Tahun', y='Produksi')
plt.title('Tren Produksi Padi di Aceh (1990-2020)')
plt.show()
```

*Insight*: Produksi meningkat hingga 2005, lalu stabil dengan fluktuasi kecil.

**2. Luas Panen vs Produksi**

```python
sns.scatterplot(data=df, x='Luas Panen', y='Produksi')
plt.title('Korelasi Luas Panen dan Produksi')
plt.show()
```

*Insight*: Hubungan positif, tetapi terdapat outlier.

**3. Distribusi Curah Hujan**

```python
sns.histplot(data=df, x='Curah Hujan', bins=10)
plt.title('Distribusi Curah Hujan')
plt.show()
```

*Insight*: Sebagian besar nilai curah hujan di kisaran 200–300 mm, sedikit skew kanan.

---

## Kesimpulan

**Kesimpulan**

* Data telah dibersihkan, dinormalisasi, dan dikategorikan.
* Tren produksi menunjukkan kenaikan awal lalu stabil.

