Materi Minggu 6: Distributed File System

AI yang digunakan: Claude (Anthropic)

Prompt: "Saya butuh bantuan untuk mencari sumber dan menyusun rangkuman materi minggu 6 dari slide kuliah."

Bagian yang disesuaikan dari keluaran prompt ada pada: saya memeriksa isi rangkuman terhadap slide kuliah, merapikan susunan dan kalimat, serta menulis bagian "Pandangan Pribadi" dengan pendapat saya sendiri.

---

# Rangkuman Minggu 6: Distributed File System

## 1. Tujuan Pembelajaran

1. Menjelaskan pengertian file system, jenisnya, dan cara kerjanya secara umum.
2. Menjelaskan pengertian distributed file system (DFS), jenisnya, dan cara kerjanya.
3. Melakukan instalasi salah satu DFS untuk memahami cara kerjanya.
4. Memahami cloud storage dan hubungannya dengan DFS.
5. Melakukan instalasi salah satu object storage sebagai cloud storage.

## 2. Pengertian File System

File system (FS) adalah metode yang dipakai sistem operasi untuk mengelola dan mengatur file pada media penyimpan seperti harddisk, flashdisk, dan SSD.

Contoh FS:

- **Linux**: ext4, XFS, btrfs, ReiserFS
- **DOS dan Windows**: FAT16, FAT32, NTFS
- **Mac**: APFS (Apple File System)

FS diakses lewat driver file system yang dikelola kernel, yang menyediakan virtual file system untuk diakses aplikasi di user space. Alurnya: aplikasi, system call interface, virtual FS, driver kernel, lalu hardware.

FS berbeda dengan hierarki FS. Hierarki adalah pengaturan file dan direktori di suatu OS, dan pengguna hanya melihat hierarki ini tanpa peduli FS yang dipakai.

## 3. Pengertian Distributed File System

DFS bukan sekadar FS yang tersebar di lebih dari satu mesin. Secara konseptual DFS adalah abstraksi terkoordinasi dari sekumpulan media penyimpan yang menutupi heterogenitas, kegagalan, dan konkurensi, sehingga menghasilkan jaringan penyimpanan yang terintegrasi, andal, dan berkinerja tinggi. Seperti sistem terdistribusi lain, medianya banyak tetapi tampak sebagai satu bagi pengguna.

Perbedaan dengan istilah lain:

- DFS adalah abstraksi level software yang mengelola data dalam file dan direktori yang bisa didistribusikan ke banyak node.
- SAN (Storage Area Network) menyajikan penyimpanan sebagai kumpulan block-level device yang bisa dihubungkan langsung oleh server dan diformat dengan FS sendiri. Secara konsep SAN bisa menjadi storage untuk DFS meskipun tidak disarankan. Penggunaan umumnya untuk backend database besar, editing video dan audio, serta virtualisasi skala besar.
- DFS lebih mengarah ke DAS (Direct-Attached Storage) di setiap node.

## 4. Berbagai DFS yang Umum

- Google File System
- Colossus, pengganti Google File System, dipakai di YouTube, Gmail, dan Google Docs
- CephFS
- Gluster
- HDFS (Hadoop Distributed File System)
- Lustre

## 5. Studi Kasus: HDFS

HDFS adalah file system open source yang scalable, terdistribusi, dan fault-tolerant, dirancang untuk mengelola dan menyimpan dataset besar di klaster commodity hardware. Commodity hardware adalah perangkat keras yang diproduksi massal, murah, dan mudah dipertukarkan dari berbagai vendor.

HDFS adalah komponen utama Apache Hadoop dan dipakai untuk pemrosesan data skala besar. File dibagi menjadi blok-blok, didistribusikan ke beberapa server, dan direplikasi untuk mencapai high availability. Menurut slide, Apache Hadoop 3.5.0 dikompilasi dengan JDK 17 dan bisa dijalankan dengan Java 17 (server) atau Java 17 dan 21 (klien).

Untuk memahami cara kerjanya, materi meminta praktik memakai HDFS sesuai dokumen panduan yang disertakan di EL (menggunakan-hadoop-hdfs-linux).

## 6. Cloud Storage

Cloud storage adalah layanan online yang memungkinkan pengguna menyimpan dan mengakses data di server jarak jauh lewat internet, bukan di hard drive lokal. Penyimpanan fisiknya mencakup beberapa server, kadang di beberapa lokasi, dan biasanya dimiliki dan dikelola penyedia cloud. Contoh: Amazon S3, Azure Blob Storage, dan Google Cloud Storage (GCS).

### 6.1 Relasi dengan DFS

Cloud storage adalah tempat penyimpanan data, sedangkan pengorganisasiannya dilakukan oleh DFS. Contohnya S3 bisa dipakai oleh HDFS: S3 sebagai cloud storage, sedangkan operasi file system dilakukan HDFS, dengan konfigurasi di core-site.xml.

### 6.2 Tipe cloud storage

- Object storage
- File storage
- Block storage

## 7. Pandangan Pribadi

Hal yang paling jelas bagi saya dari materi ini adalah bahwa pengguna cukup melihat satu penyimpanan, padahal di belakangnya banyak mesin yang bekerja sama. Ini mirip dengan konsep transparansi di minggu 1. Saya juga melihat bahwa HDFS menutup kelemahan hardware murah dengan replikasi, sehingga klaster bisa dibangun dari mesin biasa tanpa takut satu disk rusak membuat data hilang.
