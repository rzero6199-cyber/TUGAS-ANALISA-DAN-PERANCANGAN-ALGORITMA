### Penjelasan Program HuffmanGUICompression dan Algoritmanya

Program Anda, "Huffman Archiver", adalah sebuah aplikasi antarmuka grafis (GUI) yang dibangun menggunakan Python, Tkinter, dan `sv-ttk` untuk mengkompresi dan mendekompensi file serta folder. Fitur utamanya adalah penggunaan algoritma kompresi Huffman, dengan tambahan kemampuan enkripsi menggunakan library `cryptography`.

#### I. Struktur Program Secara Keseluruhan

1.  **`main.py`**: Ini adalah titik masuk utama aplikasi GUI Anda. Bertanggung jawab untuk:
    *   Membangun antarmuka pengguna (tombol, daftar file, area log, visualisasi).
    *   Mengelola interaksi pengguna (menambah file/folder, memilih arsip, memulai kompresi/dekompresi).
    *   Mengatur status aplikasi (daftar file untuk dikompresi, jalur arsip, status enkripsi).
    *   Meluncurkan operasi kompresi dan dekompresi dalam *thread* terpisah agar GUI tetap responsif.
    *   Memanggil fungsi dari `huffman.py` untuk operasi inti dan `visualizer.py` untuk menampilkan grafik dan pohon.
    *   Menangani input password untuk enkripsi/dekompresi.

2.  **`huffman.py` (Modul Inti)**: Modul ini berisi semua logika inti untuk algoritma kompresi dan dekompresi Huffman, serta penanganan format file arsip dan enkripsi. Ini adalah jantung dari fungsionalitas kompresi/dekompresi.

3.  **`visualizer.py`**: Modul ini (meskipun saya belum membacanya, fungsinya sudah jelas dari `main.py`) bertanggung jawab untuk membuat visualisasi, seperti perbandingan ukuran file asli dan terkompresi, serta representasi pohon Huffman.

4.  **`utils.py` dan `analyzer.py`**: File-file ini kemungkinan berisi fungsi-fungsi utilitas tambahan atau alat untuk menganalisis efisiensi kompresi, meskipun detailnya tidak terlihat di `main.py` atau `huffman.py`.

#### II. Algoritma Kompresi Huffman

Algoritma Huffman adalah teknik kompresi data *lossless* yang bekerja dengan membangun kode panjang variabel untuk setiap simbol (dalam kasus ini, setiap byte) dalam data input. Simbol yang lebih sering muncul diberikan kode yang lebih pendek, sedangkan simbol yang jarang muncul diberikan kode yang lebih panjang, sehingga menghasilkan ukuran data yang lebih kecil secara keseluruhan.

Berikut adalah langkah-langkah detail implementasi algoritma Huffman di `src/huffman.py`:

**A. Fase Kompresi (Fungsi-fungsi dalam `_compress_data_to_bytes` dan `_build_huffman_logic_from_bytes`):**

1.  **Hitung Frekuensi (Frequency Counting)**:
    *   Untuk setiap file input (`data_bytes`), program menghitung berapa kali setiap nilai byte (0-255) muncul. Ini disimpan dalam sebuah `defaultdict` (`frequency`).
    *   Contoh: Jika data adalah `b'AAAAABBC'`, frekuensi akan menjadi `A:5, B:2, C:1`.

2.  **Bangun Antrean Prioritas (Priority Queue)**:
    *   Setiap byte unik dan frekuensinya diubah menjadi objek `HuffmanNode`. `HuffmanNode` adalah representasi pohon biner yang memiliki `byte` (nilai byte), `freq` (frekuensi), `left` (anak kiri), dan `right` (anak kanan).
    *   Node-node ini kemudian dimasukkan ke dalam antrean prioritas (menggunakan `heapq`), di mana node dengan frekuensi terendah memiliki prioritas tertinggi.

3.  **Bangun Pohon Huffman (Huffman Tree Construction)**:
    *   Program secara berulang mengeluarkan dua node dengan frekuensi terendah dari antrean prioritas.
    *   Dua node ini digabungkan menjadi node "induk" baru. Node induk ini tidak memiliki nilai byte, tetapi frekuensinya adalah jumlah frekuensi kedua anak-anaknya. Node yang baru dibuat menjadi anak kiri dan kanan dari node induk ini.
    *   Node induk baru ini kemudian dimasukkan kembali ke antrean prioritas.
    *   Proses ini berlanjut hingga hanya ada satu node tersisa di antrean, yang merupakan akar dari Pohon Huffman.

4.  **Hasilkan Kode Huffman (Huffman Code Generation)**:
    *   Setelah pohon Huffman selesai dibangun, program melintasi (traversal) pohon tersebut dari akar ke setiap daun (leaf node).
    *   Setiap kali bergerak ke kiri, kode ditambahkan '0'. Setiap kali bergerak ke kanan, kode ditambahkan '1'.
    *   Ketika mencapai daun (yang mewakili byte asli), jalur biner dari akar ke daun tersebut menjadi kode Huffman untuk byte tersebut. Kode-kode ini disimpan dalam kamus (`codes`).
    *   Contoh: `A -> 0, B -> 10, C -> 11`.

5.  **Serialisasi Pohon Huffman (Huffman Tree Serialization)**:
    *   Pohon Huffman perlu disimpan di dalam file arsip agar data dapat didekompresi nanti. Pohon diubah menjadi urutan byte menggunakan format serialisasi khusus:
        *   `'L'` diikuti dengan byte asli untuk node daun.
        *   `'I'` diikuti dengan serialisasi anak kiri dan kanan untuk node internal.
    *   Ini adalah representasi yang ringkas dari struktur pohon.

6.  **Kompresi Data Aktual (Actual Data Compression)**:
    *   Data asli (byte) dilintasi. Untuk setiap byte, kode Huffman yang sesuai diambil dari kamus `codes` dan ditambahkan ke buffer bit (`all_bits_buffer`).
    *   Buffer bit ini kemudian diubah menjadi urutan byte. Karena kode Huffman mungkin tidak pas menjadi kelipatan 8 bit, bit-bit tambahan (`padding_bits`) ditambahkan ke akhir buffer untuk mengisi byte terakhir.
    *   Bit-bit ini kemudian dikonversi menjadi `bytearray` yang merupakan data terkompresi.

**B. Fase Dekompresi (Fungsi-fungsi dalam `_decompress_data_from_bytes`):**

1.  **Deserialisasi Pohon Huffman (Huffman Tree Deserialization)**:
    *   Saat mendekode, program pertama-tama membaca urutan byte yang merepresentasikan pohon Huffman dari arsip.
    *   Menggunakan fungsi `_deserialize_huffman_tree_from_bytes_iterator`, struktur pohon Huffman direkonstruksi kembali dari byte serial yang disimpan.

2.  **Dekompresi Data (Data Decompression)**:
    *   Data terkompresi (urutan byte) dibaca dari arsip.
    *   Setiap byte data terkompresi diubah kembali menjadi urutan bit. Bit-bit padding yang ditambahkan selama kompresi dihapus.
    *   Program memulai dari akar pohon Huffman yang direkonstruksi. Untuk setiap bit dalam urutan bit data terkompresi:
        *   Jika bit adalah '0', bergerak ke anak kiri.
        *   Jika bit adalah '1', bergerak ke anak kanan.
        *   Ketika node daun tercapai, byte yang disimpan di node daun tersebut adalah byte asli yang didekompresi. Byte ini ditambahkan ke `original_data` yang sedang dibangun.
        *   Setelah byte didekompresi, proses kembali ke akar pohon untuk bit berikutnya.
    *   Proses ini berlanjut hingga semua bit dari data terkompresi telah diproses atau ukuran file asli telah tercapai.

**C. Format File Arsip (`.huff`)**:

Program Anda menggunakan format arsip kustom (`.huff`) untuk menyimpan file terkompresi. Strukturnya kira-kira sebagai berikut:

*   **Header Arsip**:
    *   `MAGIC_ARCHIVE` (8 byte): Konstanta `b'HUFFPACK'` untuk identifikasi jenis arsip.
    *   `ARCHIVE_VERSION` (1 byte): Versi arsip.
    *   `is_encrypted` (1 byte): Flag boolean (0 atau 1) menunjukkan apakah arsip dienkripsi.
    *   `salt_len` (1 byte): Panjang *salt* untuk enkripsi.
    *   `salt` (variabel, 0-255 byte): Nilai *salt* yang digunakan untuk menurunkan kunci enkripsi (jika dienkripsi).

*   **Payload (Data File Terkompresi)**:
    *   Bagian ini berisi satu atau lebih blok file. Jika arsip dienkripsi, seluruh payload ini akan dienkripsi.
    *   Setiap blok file terdiri dari:
        *   `len(rel_path_bytes)` (2 byte, unsigned short): Panjang jalur relatif file.
        *   `rel_path_bytes` (variabel): Jalur relatif file dalam arsip.
        *   `original_size` (8 byte, unsigned long long): Ukuran asli file dalam byte.
        *   `compressed_len` (8 byte, unsigned long long): Ukuran data terkompresi file ini.
        *   `padding_bits` (1 byte): Jumlah bit padding yang ditambahkan di akhir data terkompresi.
        *   `len(tree_bytes)` (4 byte, unsigned int): Panjang pohon Huffman serial untuk file ini.
        *   `tree_bytes` (variabel): Pohon Huffman yang diserialisasi untuk file ini.
        *   `comp_data` (variabel): Data aktual file yang telah dikompresi.

#### IV. Mekanisme Enkripsi

Program Anda mengintegrasikan enkripsi menggunakan library `cryptography` Python, khususnya modul `Fernet`.

1.  **Penurunan Kunci (Key Derivation)**:
    *   Ketika password diberikan, *salt* acak (16 byte) dibuat.
    *   `PBKDF2HMAC` digunakan untuk menurunkan kunci enkripsi 32-byte yang kuat dari password dan *salt*. Proses ini melibatkan banyak iterasi (`100000`) untuk mempersulit serangan *brute-force*.
    *   Kunci yang dihasilkan kemudian di-*encode* dalam format *URL-safe base64* untuk digunakan oleh `Fernet`.

2.  **Enkripsi**:
    *   Seluruh *payload* (semua blok file yang terkompresi) dienkripsi menggunakan kunci yang diturunkan dan `Fernet`.
    *   `salt` disimpan di dalam header arsip (bersama dengan *flag* `is_encrypted`) sehingga dapat digunakan kembali untuk menurunkan kunci yang sama saat dekompresi.

3.  **Dekompresi dengan Enkripsi**:
    *   Ketika arsip dienkripsi, program akan membaca *salt* dari header.
    *   Pengguna diminta untuk memasukkan password.
    *   Password dan *salt* digunakan untuk menurunkan kunci enkripsi yang sama.
    *   *Payload* yang dienkripsi kemudian didekripsi menggunakan kunci tersebut. Jika password salah, `InvalidToken` akan terpicu.

#### IV. Keunggulan dan Kekurangan Kompresi Huffman

**Keunggulan:**

*   **Lossless**: Tidak ada data yang hilang selama kompresi dan dekompresi; file yang didekompresi identik dengan file asli.
*   **Efisien**: Sangat efektif untuk data dengan distribusi karakter/byte yang tidak merata (misalnya, file teks di mana beberapa karakter muncul jauh lebih sering daripada yang lain).
*   **Adaptif**: Pohon Huffman dapat dibuat khusus untuk setiap file atau bahkan blok data dalam file, mengoptimalkan rasio kompresi untuk konten spesifik tersebut.

**Kekurangan:**

*   **Overhead Pohon**: Pohon Huffman itu sendiri harus disimpan bersama dengan data terkompresi. Untuk file yang sangat kecil, ukuran pohon ini bisa lebih besar daripada penghematan yang didapat dari kompresi data, bahkan bisa membuat file terkompresi lebih besar dari aslinya. Program Anda menangani ini dengan memisahkan pohon untuk setiap file dalam arsip.
*   **Tidak Ideal untuk Data Acak**: Untuk data dengan distribusi byte yang seragam atau sangat acak (seperti file yang sudah terkompresi atau terenkripsi), Huffman tidak akan memberikan banyak penghematan.
*   **Dekompresi Sekuensial**: Setiap bit perlu dibaca dan dilintasi melalui pohon, yang bisa relatif lambat dibandingkan beberapa metode lain yang mendekode blok data.