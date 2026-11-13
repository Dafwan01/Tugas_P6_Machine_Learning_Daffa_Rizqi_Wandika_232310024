# Tugas P6 Machine Learning: Klasifikasi dan Clustering Dataset Penyakit Jantung

Repositori ini berisi tugas P6 mata kuliah Machine Learning. Tugasnya mencakup identifikasi atribut, praproses data, klasifikasi dengan Decision Tree, dan clustering dengan DBSCAN serta Agglomerative Clustering pada dataset penyakit jantung.

| | |
|---|---|
| Nama | Daffa Rizqi Wandika |
| NPM | 232310024 |
| Kelas | TI-23-PA |

## Dataset

Dataset yang dipakai adalah `heart_modified.csv` dengan 918 baris, 11 atribut prediktor, dan 1 atribut label.

Sumber: <https://raw.githubusercontent.com/isnanmulia/lecture-datasets/main/heart_modified.csv>

| Atribut | Jenis | Keterangan |
|---|---|---|
| Age | Numerik | Usia pasien |
| Sex | Kategorik | F, M |
| ChestPainType | Kategorik | ASY, ATA, NAP, TA |
| RestingBP | Numerik | Tekanan darah saat istirahat |
| Cholesterol | Numerik | Kadar kolesterol |
| FastingBS | Kategorik biner | 0, 1 |
| RestingECG | Kategorik | LVH, Normal, ST |
| MaxHR | Numerik | Detak jantung maksimum |
| ExerciseAngina | Kategorik | N, Y |
| Oldpeak | Numerik | Depresi segmen ST |
| ST_Slope | Kategorik | Down, Flat, Up |
| HeartDisease | Label (biner) | 0 = tidak sakit, 1 = sakit |

## Alur Pengerjaan

1. **Identifikasi atribut.** Jenis atribut ditentukan dari jumlah nilai unik tiap kolom.
2. **Praproses data.**
   - Memisahkan atribut prediktor (X) dan label (y).
   - Mengisi missing value (RestingBP, Cholesterol, dan MaxHR masing-masing 18 data) dengan `SimpleImputer(strategy='mean')`.
   - Mengubah skala atribut numerik dengan `StandardScaler` (FastingBS tidak diubah karena biner).
   - *One-Hot Encoding* pada atribut kategorik, sehingga X menjadi 15 kolom.
   - Membandingkan statistik deskriptif sebelum dan sesudah praproses.
3. **Klasifikasi.** Decision Tree dengan holdout 85% training dan 15% testing.
4. **Clustering.** DBSCAN (percobaan) dan Agglomerative Clustering (model akhir), dievaluasi dengan SSE dan silhouette score.

## Hasil

### Klasifikasi (Decision Tree, `max_depth=3`, `random_state=42`)

Data training 780 baris dan data testing 138 baris (dibagi dengan `stratify`).

Akurasi: **79,71%**

| | Precision | Recall | F1-score | Jumlah data |
|---|---|---|---|---|
| 0 (tidak sakit) | 0,75 | 0,82 | 0,78 | 62 |
| 1 (sakit) | 0,84 | 0,78 | 0,81 | 76 |

Confusion matrix:

| Aktual \ Prediksi | Tidak sakit | Sakit |
|---|---|---|
| Tidak sakit | 51 | 11 |
| Sakit | 17 | 59 |

### Clustering

**DBSCAN (percobaan).** Kombinasi terbaik adalah `eps=1.5` dan `min_samples=10` dengan 2 klaster, silhouette 0,1806, dan SSE 2269,8575. Namun, 438 data (sekitar 48%) dianggap noise, sehingga model ini tidak dipilih sebagai model akhir.

**Agglomerative Clustering (model akhir).** Memakai `linkage='ward'`. Jumlah klaster dipilih dari silhouette tertinggi pada k = 2 sampai 8.

| Metrik | Nilai |
|---|---|
| Jumlah klaster | 3 |
| SSE | 4459,4232 |
| Silhouette score | 0,1904 |
| Jumlah data per klaster | 371, 377, 170 |

Profil klaster (rata-rata dari data sebelum penskalaan):

| Klaster | Jumlah | Age | Cholesterol | MaxHR | Oldpeak | HeartDisease = 1 |
|---|---|---|---|---|---|---|
| 0 | 371 | 57,37 | 247,36 | 127,57 | 1,62 | 79,5% |
| 1 | 377 | 48,51 | 241,27 | 152,63 | 0,20 | 17,0% |
| 2 | 170 | 56,18 | 1,09 | 122,25 | 0,81 | 87,6% |

## Catatan

- Silhouette score sekitar 0,19 menunjukkan struktur klaster yang lemah.
- Kolom Cholesterol (dan satu baris RestingBP) memiliki nilai 0 yang secara medis tidak wajar dan kemungkinan merupakan data yang tidak tercatat. Nilai ini tidak diubah, sehingga klaster 2 terbentuk terutama oleh kolesterol bernilai 0, dan nilai tersebut juga muncul pada pemisahan Decision Tree.
- Praproses dilakukan pada seluruh data sebelum dibagi menjadi data training dan testing, mengikuti alur soal.

## Cara Menjalankan

Kebutuhan: Python 3 dengan `numpy`, `pandas`, `scikit-learn`, `matplotlib`, dan `seaborn`.

```bash
git clone <URL-REPOSITORI>
cd <NAMA-FOLDER>

python -m venv .venv
source .venv/bin/activate        # fish: source .venv/bin/activate.fish
pip install numpy pandas scikit-learn matplotlib seaborn

python App.py
```

Kode juga bisa dijalankan di Google Colab atau Jupyter Notebook tanpa perubahan. Sesuaikan nama berkas di perintah di atas dengan nama berkas di repositori ini.
