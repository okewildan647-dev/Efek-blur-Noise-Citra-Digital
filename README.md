import pypandoc

readme = r"""# Citra Digital dengan Python

## 1. Deskripsi Proyek
`Citra_Digital.ipynb` adalah notebook Python untuk mempelajari dasar pengolahan citra digital. Kode di dalamnya membaca sebuah gambar, memeriksa informasi gambar dan nilai pikselnya, memvisualisasikan kanal warna, memvalidasi data piksel menggunakan Pillow dan NumPy, lalu menerapkan dua efek: **Gaussian Noise** dan **Gaussian Blur**.

Notebook ini cocok untuk latihan pengenalan citra digital di Jupyter Notebook atau Google Colab.

## 2. Tujuan
- Membaca file gambar menggunakan Python.
- Memahami dimensi, format, mode warna, kanal, dan nilai piksel.
- Mengubah gambar menjadi array NumPy untuk dianalisis.
- Menampilkan gambar asli, kanal warna, dan representasi grayscale.
- Memeriksa kesesuaian data piksel Pillow dengan NumPy.
- Membandingkan gambar asli dengan hasil Gaussian Noise dan Gaussian Blur.

## 3. Pustaka yang Digunakan

```python
import numpy as np
import pandas as pd
import matplotlib.pyplot as plt
from pathlib import Path
from PIL import Image, UnidentifiedImageError, ImageFilter
```

- **NumPy (`np`)**: mengubah gambar menjadi array dan melakukan operasi numerik pada piksel.
- **Pandas (`pd`)**: menyusun metadata dan hasil pemeriksaan ke dalam tabel.
- **Matplotlib (`plt`)**: menampilkan gambar dan perbandingan hasil pemrosesan.
- **Pillow (`PIL`)**: membuka, menyalin, mengonversi, serta memproses gambar.
- **Pathlib (`Path`)**: memeriksa dan mengelola lokasi file gambar.
- **UnidentifiedImageError**: membantu menangani file yang formatnya tidak dapat dikenali oleh Pillow.
- **ImageFilter**: menyediakan filter gambar, termasuk Gaussian Blur.

## 4. Penjelasan Kode Berdasarkan Urutan Sel

### A. Import pustaka
Kode mengimpor NumPy, Pandas, dan Matplotlib sebagai alat utama untuk analisis numerik, pembuatan tabel, serta visualisasi.

### B. Membaca gambar dan menangani kesalahan
Variabel `IMAGE_PATH` menyimpan lokasi gambar yang akan diproses. `Path(IMAGE_PATH).expanduser()` membentuk objek path dan menangani simbol direktori home jika digunakan.

Sebelum gambar dibuka, `is_file()` memeriksa apakah file tersedia. Jika tidak ditemukan, kode menghasilkan `FileNotFoundError` dengan pesan yang membantu pengguna memeriksa lokasi file. Gambar kemudian dibuka menggunakan `Image.open()`, dimuat sepenuhnya, dan disalin agar objek gambar dapat digunakan setelah blok `with` selesai. Format file yang terdeteksi disimpan pada `detected_format`.

Kode juga menangani format yang tidak dikenali (`UnidentifiedImageError`) dan kesalahan baca file (`OSError`).

### C. Mengambil metadata gambar
Kode mengambil beberapa informasi penting:
- `image.size`: lebar dan tinggi gambar.
- `image.mode`: mode warna asli, misalnya RGB, RGBA, L, CMYK, atau P.
- `np.asarray(image)`: representasi data gambar sebagai array NumPy.
- `image.getbands()`: kanal atau komponen pada mode gambar.
- `width * height`: jumlah total piksel.
- `original_array.shape`: bentuk/dimensi array.
- `original_array.dtype`: tipe data elemen array.
- `original_array.min()` dan `.max()`: nilai minimum dan maksimum yang ditemukan.

Informasi tersebut disusun menjadi `metadata`, kemudian dicetak sebagai ringkasan.

### D. Memahami mode warna
Dictionary `mode_explanations` memberikan penjelasan singkat tentang beberapa mode gambar:
- **RGB**: kanal merah, hijau, dan biru.
- **RGBA**: RGB ditambah kanal Alpha untuk transparansi.
- **L**: satu kanal intensitas grayscale.
- **LA**: grayscale dengan Alpha.
- **CMYK**: Cyan, Magenta, Yellow, dan Key/Black.
- **P**: nilai array berupa indeks ke palet warna.
- **1**: citra biner hitam-putih.

Kode menampilkan penjelasan sesuai `image.mode`. Penanganan ini penting karena susunan nilai piksel berbeda tergantung mode gambar.

### E. Memeriksa bentuk array dan nilai piksel
`original_array.shape` menunjukkan susunan data gambar. Umumnya, citra grayscale memiliki bentuk `(tinggi, lebar)`, sedangkan citra berkanal memiliki bentuk `(tinggi, lebar, kanal)`.

Kode menampilkan sampel matriks di sudut kiri atas, maksimal 5 × 5 piksel. Untuk mode RGB/RGBA atau CMYK, kode juga menampilkan sampel masing-masing kanal. Selanjutnya, kode memeriksa nilai piksel pada koordinat `(x=10, y=10)` apabila koordinat tersebut berada di dalam batas gambar. Jika ukuran gambar terlalu kecil, pesan akan ditampilkan sebagai gantinya.

### F. Visualisasi gambar, kanal, dan grayscale
Kode menampilkan gambar asli menggunakan Matplotlib. Untuk visualisasi, gambar CMYK dikonversi ke RGB, sedangkan gambar mode P dikonversi ke RGB atau RGBA sesuai informasi transparansi. Konversi tersebut digunakan untuk tampilan; data asli `image` tetap dipertahankan untuk analisis.

Kode berikutnya memvisualisasikan setiap kanal warna secara terpisah, misalnya Red, Green, Blue, dan Alpha untuk gambar RGB/RGBA. Terakhir, `image.convert('L')` membuat representasi grayscale untuk ditampilkan dalam warna abu-abu.

### G. Memvalidasi nilai piksel dengan Pillow dan NumPy
Kode memilih beberapa koordinat, yaitu sudut gambar dan titik tengah; koordinat `(10, 10)` juga ditambahkan jika ukuran gambar mencukupi. Pada setiap koordinat, kode membandingkan:
- nilai dari `image.getpixel((px, py))` milik Pillow;
- nilai dari `original_array[py, px]` milik NumPy.

`np.array_equal()` digunakan untuk menentukan apakah kedua nilai sama. Hasilnya dimasukkan ke DataFrame `checks_df`. Kode juga membandingkan keseluruhan data gambar dengan `np.array_equal(np.asarray(image), original_array)` dan menampilkan status `VALID` atau `TIDAK VALID`.

### H. Menampilkan tabel ringkasan dan kesimpulan
DataFrame `summary` merangkum format gambar, mode warna, dimensi, jumlah piksel, bentuk array, jumlah kanal, tipe data, serta rentang nilai piksel. Bagian akhir analisis mencetak ringkasan dalam bentuk kalimat, termasuk catatan khusus untuk mode P dan CMYK.

### I. Memuat ulang gambar untuk proses efek
Pada bagian efek, pustaka diimpor kembali dan gambar dibuka dari `IMAGE_PATH`, kemudian dikonversi menjadi RGB:

```python
img = Image.open(IMAGE_PATH).convert("RGB")
img_array = np.array(img)
```

`img` adalah gambar RGB yang digunakan oleh Pillow, sedangkan `img_array` adalah representasi array NumPy. Kode kemudian menampilkan foto asli sebagai acuan perbandingan.

### J. Menambahkan Gaussian Noise
Pertama, array gambar dikonversi ke tipe `float32` agar operasi penambahan noise dapat dilakukan dengan angka pecahan.

```python
image_float = img_array.astype(np.float32)
mean = 0
sigma = 25
noise = np.random.normal(mean, sigma, image_float.shape)
noisy_image = image_float + noise
noisy_image = np.clip(noisy_image, 0, 255).astype(np.uint8)
```

`np.random.normal()` menghasilkan noise acak yang mengikuti distribusi normal. `mean = 0` berarti rata-rata noise ditetapkan nol, sedangkan `sigma = 25` mengatur simpangan baku atau tingkat variasi noise. Noise ditambahkan ke nilai piksel gambar. `np.clip()` membatasi nilai agar tetap berada pada rentang 0–255, lalu `astype(np.uint8)` mengubahnya menjadi tipe data piksel 8-bit.

Terakhir, Matplotlib menampilkan foto asli dan foto dengan Gaussian Noise berdampingan. Karena noise bersifat acak, hasil dapat berubah setiap kali sel dijalankan.

### K. Menambahkan Gaussian Blur
Kode berikut menerapkan filter Gaussian Blur pada objek gambar:

```python
blur_image = img.filter(ImageFilter.GaussianBlur(radius=5))
```

`radius=5` menentukan tingkat blur yang digunakan. Efek ini membuat gambar tampak lebih halus dengan mengurangi detail-detail kecil. Kode kemudian menampilkan gambar asli dan hasil blur secara berdampingan agar perbedaannya mudah diamati.

## 5. Cara Menjalankan

1. Buka `Citra_Digital.ipynb` di Google Colab atau Jupyter Notebook.
2. Pastikan pustaka yang dibutuhkan sudah terpasang. Jika perlu, jalankan:

   ```bash
   pip install numpy pandas matplotlib pillow
   ```

3. Siapkan file gambar yang ingin dianalisis.
4. Ubah nilai `IMAGE_PATH` di **kedua bagian kode** yang membukanya agar sesuai dengan lokasi gambar Anda.
5. Jalankan sel notebook dari atas ke bawah.

Contoh path yang saat ini tertulis di notebook:

```python
IMAGE_PATH = "/content/drive/MyDrive/citra digital/JKW.jfif"
```

Path tersebut merupakan contoh lokasi di Google Colab/Google Drive. Jika menjalankan notebook di komputer sendiri, ganti dengan path lokal yang benar. Jika menggunakan Google Drive di Colab, pastikan Drive sudah dihubungkan dan file memang berada di folder tersebut.

## 6. Hasil yang Diharapkan
Setelah semua sel berhasil dijalankan, notebook akan:
- menampilkan metadata dan mode warna gambar;
- menunjukkan sampel matriks serta nilai piksel;
- menampilkan gambar asli, kanal warna, dan grayscale;
- menghasilkan tabel pemeriksaan piksel Pillow vs NumPy;
- menampilkan tabel ringkasan citra;
- memperlihatkan perbandingan gambar asli dengan Gaussian Noise;
- memperlihatkan perbandingan gambar asli dengan Gaussian Blur.

## 7. Struktur File

```text
Citra-Digital/
├── Citra_Digital.ipynb   # Notebook berisi kode pengolahan citra
├── README.md             # Dokumentasi proyek
└── JKW.jfif              # Contoh gambar, jika menggunakan file ini
```

Nama file gambar dan struktur folder dapat berbeda. Yang penting, `IMAGE_PATH` harus menunjuk ke file gambar yang benar.

## 8. Catatan
- Notebook menggunakan dua bagian pembacaan gambar, sehingga path `IMAGE_PATH` perlu disesuaikan pada keduanya.
- Data gambar asli dianalisis sesuai mode aslinya, sedangkan bagian penerapan noise dan blur mengubah gambar menjadi RGB terlebih dahulu.
- Gaussian Noise menambahkan variasi acak pada piksel; Gaussian Blur menghaluskan gambar. Keduanya menghasilkan efek yang berbeda.
- Nilai `sigma` dan `radius` dapat diubah untuk bereksperimen dengan intensitas efek.
- Dokumentasi ini menjelaskan perilaku yang tertulis pada kode notebook; hasil aktual bergantung pada gambar yang digunakan dan keberhasilan eksekusi setiap sel.
"""
output_path = "/mnt/data/README.md"
pypandoc.convert_text(readme, "md", format="md", outputfile=output_path, extra_args=["--standalone"])
print(f"README.md penjelasan kode berhasil dibuat: {output_path}")

