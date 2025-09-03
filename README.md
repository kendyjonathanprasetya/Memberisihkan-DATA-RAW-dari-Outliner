📌 Pendahuluan

Dalam pengolahan data, sering kali terdapat nilai ekstrem yang jauh berbeda dari pola mayoritas. Nilai ini disebut outlier. Kehadiran outlier bisa mengganggu analisis, membuat rata-rata tidak representatif, dan merusak visualisasi (diagram jadi “melebar” karena skala ekstrem).
Oleh karena itu, salah satu langkah penting dalam data preprocessing adalah deteksi dan penanganan outlier.

Salah satu metode populer untuk mendeteksi outlier adalah Interquartile Range (IQR).

📌 Penjelasan Metode IQR

Interquartile Range (IQR) adalah metode berbasis statistik yang memanfaatkan kuartil.

Hitung Q1 dan Q3

Q1 = kuartil pertama (25% data terendah).

Q3 = kuartil ketiga (75% data terendah).

Hitung IQR

IQR = Q3 – Q1

IQR menunjukkan rentang data tengah (50% data).

Tentukan batas bawah dan atas

Batas bawah = Q1 – 1.5 × IQR

Batas atas = Q3 + 1.5 × IQR

Deteksi outlier

Data < batas bawah → outlier rendah

Data > batas atas → outlier tinggi

📌 Penanganan Outlier

Ada beberapa cara menangani outlier:

Menghapus baris outlier → sederhana, tapi berisiko mengurangi data terlalu banyak.

Mengganti outlier dengan rata-rata (mean) → menjaga ukuran dataset, tapi rata-rata sensitif terhadap outlier lain.

Mengganti dengan median → lebih stabil, sering dipakai saat distribusi data miring.

Mengganti dengan batas bawah/atas IQR → membuat data tetap dalam rentang wajar.

Dalam kasusmu, dipilih cara mengganti outlier (baik terlalu tinggi maupun rendah) dengan rata-rata data normal, agar jumlah data tidak berkurang dan distribusi tetap representatif.
# Data Raw
[Download data.csv](https://github.com/kendyjonathanprasetya/Memberisihkan-DATA-RAW-dari-Outliner/blob/main/data%20tiktok%20csv.csv)

# Data Clean
[Download data.csv](https://github.com/kendyjonathanprasetya/Memberisihkan-DATA-RAW-dari-Outliner/blob/main/data_tiktok_clean.csv)

# Diagram Data Raw
<img width="819" height="441" alt="image" src="https://github.com/user-attachments/assets/4f0034ae-23f5-414a-a32c-b3066fd3cfb7" />

# Diagram Data Clean
<img width="818" height="446" alt="image" src="https://github.com/user-attachments/assets/a8dcdee5-4972-4247-8ae1-3a86e81ac440" />







import pandas as pd
import matplotlib.pyplot as plt


df = pd.read_csv("data tiktok csv.csv")


def iqr_outlier_mask(series):
    Q1 = series.quantile(0.25)
    Q3 = series.quantile(0.75)
    IQR = Q3 - Q1
    lower = Q1 - 1.5 * IQR
    upper = Q3 + 1.5 * IQR
    return (series < lower) | (series > upper)


num_cols = ["Views", "Likes", "Comments", "Shares", "AvgViewerAge"]


df_clean = df.copy()


for col in num_cols:
    mask = iqr_outlier_mask(df_clean[col])
    mean_val = df_clean[col][~mask].mean()
    df_clean.loc[mask, col] = round(mean_val)  # dibulatkan supaya rapi


df.to_excel("data_tiktok_raw.xlsx", index=False)
df_clean.to_excel("data_tiktok_clean.xlsx", index=False)


print("=== DATA RAW ===")
print(df)
print("\n=== DATA CLEAN (outlier diganti mean) ===")
print(df_clean)


plt.figure(figsize=(12,6))
plt.bar(df["VideoID"], df["Views"], color="skyblue")
plt.title("Diagram Batang Views - RAW")
plt.xlabel("Video ID")
plt.ylabel("Jumlah Views")
plt.show()


plt.figure(figsize=(12,6))
plt.bar(df_clean["VideoID"], df_clean["Views"], color="salmon")
plt.title("Diagram Batang Views - CLEAN (Outlier diganti mean)")
plt.xlabel("Video ID")
plt.ylabel("Jumlah Views")
plt.show()
