# Aplikasi Desktop Kompresi dan Dekompresi File Universal menggunakan Algoritma Huffman Coding

[![Python 3.x](https://img.shields.io/badge/Python-3.x-blue.svg)](https://www.python.org/)
[![GUI - Tkinter/PyQt](https://img.shields.io/badge/GUI-Tkinter%2FPyQt-green.svg)](https://docs.python.org/3/library/tkinter.html)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)

## Daftar Isi

1.  [Pendahuluan](#pendahuluan)
2.  [Fitur](#fitur)
3.  [Struktur Proyek](#struktur-proyek)
4.  [Instalasi](#instalasi)
5.  [Penggunaan](#penggunaan)
6.  [Analisis Performa](#analisis-performa)
7.  [Pengujian](#pengujian)
8.  [Kontribusi](#kontribusi)
9.  [Lisensi](#lisensi)

## 1. Pendahuluan

Proyek ini adalah implementasi **Aplikasi Desktop Kompresi dan Dekompresi File Universal** menggunakan **Algoritma Huffman Coding** yang dibangun dari nol (tanpa menggunakan pustaka kompresi pihak ketiga). Aplikasi ini dirancang untuk memproses berbagai jenis file—teks, dokumen (seperti PDF/DOCX yang diperlakukan sebagai biner), gambar (JPG, PNG, BMP), dan file biner lainnya—pada level byte.

Tujuan utama proyek ini adalah menyediakan alat yang efisien untuk mengurangi ukuran file sekaligus memberikan pemahaman mendalam tentang prinsip kerja algoritma Huffman Coding melalui implementasi mandiri. Aplikasi ini juga dilengkapi dengan fungsionalitas analisis performa dan visualisasi untuk memenuhi standar tugas akhir semester.

## 2. Fitur

*   **Kompresi Universal:** Mampu mengkompresi file teks, dokumen, gambar, dan file biner lainnya.
*   **Dekompresi Lossless:** Mendekompresi file hasil kompresi kembali ke keadaan aslinya secara 100% identik.
*   **Algoritma Huffman dari Nol:** Implementasi Algoritma Huffman Coding murni pada level byte.
*   **Antarmuka Pengguna Grafis (GUI):** Aplikasi desktop intuitif untuk kemudahan penggunaan.
*   **Analisis Performa:** Pengukuran waktu eksekusi (kompresi & dekompresi), estimasi penggunaan memori, dan rasio kompresi.
*   **Visualisasi (Opsional):** Grafik perbandingan ukuran file dan waktu kompresi, serta visualisasi struktur pohon Huffman (teks).
*   **Penanganan Kesalahan:** Robust terhadap file kosong, file tidak valid/rusak.

## 3. Struktur Proyek

```
HuffmanGUICompression/
│
├── src/
│   ├── main.py            # Entry point GUI aplikasi.
│   ├── huffman.py         # Implementasi Algoritma Huffman (encoding & decoding byte-level).
│   ├── analyzer.py        # Modul untuk analisis performa (waktu, memori, rasio).
│   ├── utils.py           # Utilitas umum, helper fungsi (misal: untuk file I/O).
│   └── visualizer.py      # Modul untuk fungsi visualisasi grafik dan pohon Huffman.
│
├── data/
│   ├── input/             # Contoh file input untuk pengujian.
│   └── output/            # Lokasi penyimpanan file hasil kompresi/dekompresi.
│
├── tests/
│   └── test_cases.md      # Dokumentasi mendetail tentang skenario pengujian.
│
├── docs/
│   └── analysis_results.txt # Hasil analisis performa (opsional, bisa diganti laporan PDF).
│
├── todo-list.md           # Daftar tugas proyek.
├── prp.md                 # Proposal Riset Proyek.
├── aturan.md              # Aturan dan Pedoman Pengembangan Proyek.
└── README.md              # Dokumen ini.
```

## 4. Instalasi

Untuk menjalankan aplikasi ini, Anda memerlukan:
*   Python 3.x
*   Pustaka Tkinter (biasanya sudah termasuk dalam instalasi Python standar) atau PyQt/CustomTkinter (jika dipilih)
*   Pustaka `matplotlib` (untuk visualisasi)

**Langkah-langkah Instalasi:**

1.  **Clone repositori ini:**
    ```bash
    git clone https://github.com/yourusername/HuffmanGUICompression.git
    cd HuffmanGUICompression
    ```
    *(Catatan: Asumsi repositori Git, jika tidak, cukup buat folder dan tempatkan file di sana)*

2.  **Buat virtual environment (direkomendasikan):**
    ```bash
    python -m venv venv
    .\venv\Scripts\activate   # Di Windows
    source venv/bin/activate  # Di macOS/Linux
    ```

3.  **Instal dependensi:**
    ```bash
    pip install matplotlib
    ```
    *(Jika menggunakan PyQt/CustomTkinter, sesuaikan dengan instalasi pustaka tersebut)*

## 5. Penggunaan

Untuk menjalankan aplikasi GUI:

```bash
python src/main.py
```

**Langkah-langkah Penggunaan Aplikasi:**

1.  **Pilih File Input:** Klik tombol "Pilih File..." dan navigasikan ke file yang ingin Anda kompresi.
2.  **Kompresi:** Klik tombol "Kompresi...". Aplikasi akan mengkompresi file dan menyimpannya di path output yang disarankan (dengan ekstensi `.huff`). Hasil kompresi dan analisis performa akan ditampilkan.
3.  **Dekompresi:** Klik tombol "Dekompresi...". Aplikasi akan mendekode file `.huff` dan menyimpannya sebagai file asli di path output yang disarankan.

## 6. Analisis Performa

Aplikasi ini menyediakan metrik performa berikut di GUI setelah operasi kompresi/dekompresi:
*   Waktu eksekusi (milidetik).
*   Ukuran file asli dan terkompresi (byte).
*   Rasio kompresi dan persentase penghematan (%).
*   (Opsional) Grafik perbandingan ukuran dan waktu kompresi.

<h2> 7. Pengujian </h2>

Detail mengenai test cases yang dirancang untuk memverifikasi fungsionalitas dan akurasi aplikasi dapat ditemukan di [tests/test_cases.md](tests/test_cases.md). Verifikasi integritas file hasil dekompresi dengan file asli dilakukan menggunakan checksum SHA256.

<h2> 8. Kontribusi </h2>

Jika Anda ingin berkontribusi pada proyek ini, silakan ikuti panduan kontribusi standar.

<h2> 9. Lisensi </h2>

Proyek ini dilisensikan di bawah [Lisensi MIT](LICENSE).

---
