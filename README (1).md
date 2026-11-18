# Sistem Monitoring Stok dan Penjualan Kantin Kampus

## Identitas

| Keterangan   | Isi                                                |
|--------------|----------------------------------------------------|
| Nama Lengkap | [Hermawati]                                 |
| NIM          | [C2583207038]                                          |
| Kelas        | [PTI3B]                                    |
| Mata Kuliah  | Algoritma dan Pemrograman Lanjutan                 |
| Dosen        | Taofik Muhammad, M.Kom                             |
| Nama Proyek  | Sistem Monitoring Stok dan Penjualan Kantin Kampus |

## Deskripsi Singkat

Aplikasi console berbahasa C untuk membantu pengelola kantin kampus mencatat produk, memantau stok, memproses transaksi penjualan, serta melihat laporan penjualan. Seluruh data disimpan di dalam array selama program berjalan (tanpa database atau file eksternal). Kapasitas maksimal 50 produk dengan 6 produk sebagai data awal (Nasi Goreng, Mie Ayam, Es Teh Manis, Gorengan, Air Mineral, Kopi Susu).

## Daftar Fitur

1. Lihat Daftar Produk (nama, harga, stok)
2. Tambah Produk
3. Ubah Produk (harga dan stok)
4. Transaksi Penjualan (beberapa jenis produk dalam satu transaksi)
5. Tambah Stok
6. Cari Produk berdasarkan nama
7. Urutkan Produk (harga termurah dan jumlah terjual terbanyak)
8. Laporan Penjualan (jumlah terjual per produk, total unit, total pendapatan, produk terlaris)
9. Cek Stok Menipis (stok < 5)
0. Keluar

Validasi input: pilihan menu tidak tersedia atau bukan angka, nomor produk tidak ditemukan, nama produk kosong atau sudah ada, jumlah beli nol/negatif, harga/stok negatif, jumlah beli melebihi stok, dan kapasitas array penuh.

## Penjelasan Penggunaan Array

Data disimpan dalam array dengan indeks yang saling berkaitan (indeks yang sama = produk yang sama).

| Array | Tipe | Fungsi |
|-------|------|--------|
| `nama` | array karakter 2 dimensi (`char`) | Menyimpan nama produk |
| `harga` | array 1 dimensi (`int`) | Menyimpan harga produk |
| `stok` | array 1 dimensi (`int`) | Menyimpan stok produk |
| `terjual` | array 1 dimensi (`int`) | Menyimpan jumlah terjual |
| `pendapatan` | array 1 dimensi (`long`) | Menyimpan akumulasi pendapatan per produk |
| `jumlah` | integer | Jumlah data produk aktif (indeks 0 sampai `jumlah` - 1) |

Deklarasi pada kode:

```c
#define MAX_PRODUK    50
#define PANJANG_NAMA  30
#define BATAS_MENIPIS 5

char nama[MAX_PRODUK][PANJANG_NAMA];
int  harga[MAX_PRODUK];
int  stok[MAX_PRODUK];
int  terjual[MAX_PRODUK];
long pendapatan[MAX_PRODUK];
int  jumlah = 0;
```

Array bantu lokal yang dipakai di dalam fungsi:
- `qtyBeli[MAX_PRODUK]` pada transaksi, untuk menampung jumlah beli per produk sebelum transaksi dikonfirmasi.
- `urut[MAX_PRODUK]` pada pengurutan, untuk menampung urutan indeks produk.

**Cara kerja singkat:**
- Produk baru ditambahkan pada indeks `jumlah`, lalu `jumlah` bertambah satu.
- Penambahan ditolak jika `jumlah` sudah mencapai 50 (`MAX_PRODUK`).
- Transaksi: jumlah beli dicatat dulu di `qtyBeli`. Stok, `terjual`, dan `pendapatan` baru diperbarui setelah pengguna mengonfirmasi `y`. Jika stok tidak cukup (termasuk jumlah yang sudah dipilih pada transaksi yang sama), pembelian produk itu ditolak.
- Pencarian: sequential search. Program memeriksa array `nama` dari indeks 0 sampai `jumlah` - 1. Kata kunci dan nama produk diubah ke huruf kecil, lalu dicocokkan dengan `strstr()` sehingga potongan kata sudah cukup dan huruf besar/kecil tidak dibedakan. Contoh: input `kopi` menemukan "Kopi Susu".
- Pengurutan: bubble sort pada array indeks `urut[]`, bukan pada array data. Mode 1 mengurutkan berdasarkan `harga` dari termurah, mode 2 berdasarkan `terjual` dari terbanyak. Karena yang ditukar hanya indeks di `urut[]`, array `nama`, `harga`, `stok`, `terjual`, dan `pendapatan` tidak berubah dan tetap sinkron. Kolom No pada hasil urutan tetap memakai nomor produk asli. Produk dengan nilai yang sama tetap berurutan sesuai nomor aslinya (contoh: Kopi Susu No 6 tampil sebelum Ayam geprek No 7 karena sama-sama 8000).

## Cara Kompilasi dan Menjalankan

Prasyarat: compiler GCC.

```bash
gcc main.c -o kantin
```

Menjalankan di Linux/Mac:

```bash
./kantin
```

Menjalankan di Windows:

```bash
kantin.exe
```

Alternatif: buka file `main.c` di Code::Blocks, lalu tekan **F9** (Build and Run).

## Pengujian

Data awal: Nasi Goreng (12000, stok 20), Mie Ayam (10000, stok 15), Es Teh Manis (4000, stok 30), Gorengan (2000, stok 40), Air Mineral (3000, stok 4), Kopi Susu (8000, stok 12).

### Skenario 1: Tambah Produk
- **Input/Tindakan:** Menu 2, masukkan produk baru: Ayam geprek, harga 8000, stok awal 40. Lalu buka daftar produk (menu 1).
- **Hasil yang diharapkan:** Produk muncul dalam daftar.
- **Keterangan:** Daftar awal berisi 6 produk. Setelah menu 2 dijalankan, program menampilkan pesan `Produk "Ayam geprek" berhasil ditambahkan.` dan Ayam geprek muncul sebagai produk nomor 7 (harga 8000, stok 40) pada daftar produk di skenario berikutnya.

<img width="1600" height="900" alt="WhatsApp Image 2026-11-17 at 12 51 20" src="https://github.com/user-attachments/assets/4f07a25f-69bb-48f9-b1c7-f0348bea3ad9" />


### Skenario 2: Transaksi
- **Input/Tindakan:** Menu 4, beli: Mie Ayam x 4, Air Mineral x 2, Ayam geprek x 3, lalu masukkan 0 untuk selesai dan konfirmasi `y`.
- **Hasil yang diharapkan:** Subtotal dan total benar.
- **Keterangan:** Hitungan manual: Mie Ayam 4 x 10000 = 40000; Air Mineral 2 x 3000 = 6000; Ayam geprek 3 x 8000 = 24000. Total = 40000 + 6000 + 24000 = 70000, sesuai tampilan program.

<img width="1600" height="900" alt="WhatsApp Image 2026-11-17 at 12 51 21" src="https://github.com/user-attachments/assets/f016b453-6d3f-4d7e-98ac-97128c1cd39e" />


### Skenario 3: Pembaruan Stok
- **Input/Tindakan:** Selesaikan transaksi pada skenario 2, lalu buka menu 1.
- **Hasil yang diharapkan:** Stok dan jumlah terjual berubah.
- **Keterangan:** Sebelum transaksi: Mie Ayam 15, Air Mineral 4, Ayam geprek 40 (daftar produk di awal menu 4). Sesudah transaksi (menu 1): Mie Ayam 11, Air Mineral 2, Ayam geprek 37. Produk lain tidak berubah. Jumlah terjual pada menu 8 ikut bertambah (Mie Ayam 4, Air Mineral 2, Ayam geprek 3).

<img width="1600" height="900" alt="3" src="https://github.com/user-attachments/assets/a9ad67a6-c81a-4bb5-a3d6-88561cdd5523" />


### Skenario 4: Stok Tidak Cukup
- **Input/Tindakan:** Menu 4, beli Es Teh Manis x 31 (stok tersedia 30), lalu masukkan 0.
- **Hasil yang diharapkan:** Transaksi ditolak; stok tetap.
- **Keterangan:** Program menampilkan `[!] Stok Es Teh Manis tidak cukup (tersedia 30, sudah dipilih 0).` lalu transaksi dibatalkan karena tidak ada produk dipilih. Setelah penolakan, laporan (menu 8) masih menunjukkan total 9 unit dan pendapatan 70000, sama seperti sebelum percobaan, sehingga tidak ada data yang berubah. Stok Es Teh Manis tetap 30.

<img width="1600" height="900" alt="4" src="https://github.com/user-attachments/assets/3a352219-b063-4ec4-b924-2a65e92c3216" />


### Skenario 5: Laporan
- **Input/Tindakan:** Menu 8.
- **Hasil yang diharapkan:** Unit, pendapatan, dan produk terlaris benar.
- **Keterangan:** Total unit terjual 4 + 2 + 3 = 9, total pendapatan 40000 + 6000 + 24000 = 70000, produk terlaris Mie Ayam (4 unit).

<img width="1600" height="900" alt="5" src="https://github.com/user-attachments/assets/807560f4-5bb3-4462-9972-2fc5432e94fa" />


### Skenario 6: Pengurutan
- **Input/Tindakan:** Menu 7, pilih urutan 1 (harga termurah), lalu ulangi dan pilih urutan 2 (jumlah terjual terbanyak).
- **Hasil yang diharapkan:** Harga dari termurah; jumlah terjual dari terbanyak.
- **Keterangan:** Urutan harga dimulai dari Gorengan (2000) sampai Nasi Goreng (12000). Urutan terjual dimulai dari Mie Ayam (4), Ayam geprek (3), Air Mineral (2), lalu produk yang belum terjual (0).

<img width="1600" height="900" alt="6" src="https://github.com/user-attachments/assets/b8e544da-2285-4bd1-af85-07f7884e2af5" />

<img width="1600" height="900" alt="7" src="https://github.com/user-attachments/assets/6c611987-9c78-4c49-8682-3f60e0bf4d14" />


### Skenario 7: Pencarian dan Stok
- **Input/Tindakan:** Menu 6 cari produk: `kopi`. Lalu menu 9 untuk cek stok < 5.
- **Hasil yang diharapkan:** Hasil pencarian dan peringatan stok menipis tepat.
- **Keterangan:** Pencarian "kopi" menemukan 1 produk (Kopi Susu, 8000, stok 12). Menu 9 menampilkan peringatan untuk Air Mineral dengan stok 2.

<img width="1600" height="900" alt="8" src="https://github.com/user-attachments/assets/8df6b0cd-6b41-49d4-beb3-842603d411d9" />


## Struktur Repository

```
Proyek-Array-C-[NIM]/
├── main.c
├── README.md
└── screenshots/
    ├── 01-tambah-produk.png
    ├── 02-transaksi.png
    ├── 03a-stok-sebelum.png
    ├── 03b-stok-sesudah.png
    ├── 04a-stok-tidak-cukup.png
    ├── 04b-stok-tetap.png
    ├── 05-laporan.png
    ├── 06a-urut-harga.png
    ├── 06b-urut-terjual.png
    ├── 07a-cari-produk.png
    └── 07b-stok-menipis.png
```
