# Memberisihkan-DATA-RAW-dari-Outliner

# Data Raw
[Download data.csv](https://github.com/kendyjonathanprasetya/Memberisihkan-DATA-RAW-dari-Outliner/blob/main/data%20tiktok%20csv.csv)

# Data Clean

[Download data.csv]9https://github.com/kendyjonathanprasetya/Memberisihkan-DATA-RAW-dari-Outliner/blob/main/data_tiktok_clean.csv)





import pandas as pd
import matplotlib.pyplot as plt

# 1. Baca file RAW CSV
df = pd.read_csv("data tiktok csv.csv")

# 2. Fungsi deteksi outlier dengan IQR
def iqr_outlier_mask(series):
    Q1 = series.quantile(0.25)
    Q3 = series.quantile(0.75)
    IQR = Q3 - Q1
    lower = Q1 - 1.5 * IQR
    upper = Q3 + 1.5 * IQR
    return (series < lower) | (series > upper)

# 3. Tentukan kolom numerik
num_cols = ["Views", "Likes", "Comments", "Shares", "AvgViewerAge"]

# 4. Buat salinan data untuk CLEAN
df_clean = df.copy()

# 5. Ganti nilai outlier dengan rata-rata
for col in num_cols:
    mask = iqr_outlier_mask(df_clean[col])
    mean_val = df_clean[col][~mask].mean()
    df_clean.loc[mask, col] = round(mean_val)  # dibulatkan supaya rapi

# 6. Simpan ke Excel
df.to_excel("data_tiktok_raw.xlsx", index=False)
df_clean.to_excel("data_tiktok_clean.xlsx", index=False)

# 7. Tampilkan tabel RAW dan CLEAN di console
print("=== DATA RAW ===")
print(df)
print("\n=== DATA CLEAN (outlier diganti mean) ===")
print(df_clean)

# 8. Diagram batang RAW (contoh pakai Views)
plt.figure(figsize=(12,6))
plt.bar(df["VideoID"], df["Views"], color="skyblue")
plt.title("Diagram Batang Views - RAW")
plt.xlabel("Video ID")
plt.ylabel("Jumlah Views")
plt.show()

# 9. Diagram batang CLEAN (Views)
plt.figure(figsize=(12,6))
plt.bar(df_clean["VideoID"], df_clean["Views"], color="salmon")
plt.title("Diagram Batang Views - CLEAN (Outlier diganti mean)")
plt.xlabel("Video ID")
plt.ylabel("Jumlah Views")
plt.show()
