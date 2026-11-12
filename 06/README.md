Materi Minggu 6: Distributed File System

AI yang digunakan: Claude (Anthropic)

Prompt: "Bantu saya menyusun rangkuman materi minggu 6 dari slide, dari file system sampai cloud storage."

Bagian yang disesuaikan dari keluaran prompt ada pada: saya membaca ulang rangkuman dan menyesuaikannya dengan slide, lalu menambahkan pandangan pribadi saya.

---

# Rangkuman Minggu 6: Distributed File System

## 1. Inti Materi

File system mengatur file pada media penyimpan, sedangkan distributed file system (DFS) menyatukan banyak media penyimpan menjadi satu penyimpanan yang tampak tunggal bagi pengguna. HDFS adalah contoh DFS untuk data besar, dan cloud storage berperan sebagai tempat penyimpanan yang pengorganisasiannya bisa ditangani oleh DFS.

## 2. Tujuan Pembelajaran

1. Menjelaskan pengertian file system, jenisnya, dan cara kerjanya secara umum.
2. Menjelaskan pengertian distributed file system (DFS), jenisnya, dan cara kerjanya.
3. Melakukan instalasi salah satu DFS untuk memahami cara kerjanya.
4. Memahami cloud storage dan hubungannya dengan DFS.
5. Melakukan instalasi salah satu object storage sebagai cloud storage.

## 3. Pengertian File System

File system (FS) adalah metode yang dipakai sistem operasi untuk mengelola dan mengatur file pada media penyimpan seperti harddisk, flashdisk, dan SSD.

Contoh FS:

- **Linux**: ext4, XFS, btrfs, ReiserFS
- **DOS dan Windows**: FAT16, FAT32, NTFS
- **Mac**: APFS (Apple File System)

FS diakses lewat driver file system yang dikelola kernel, yang menyediakan virtual file system untuk diakses aplikasi di user space. Alurnya: aplikasi, system call interface, virtual FS, driver kernel, lalu hardware.

FS berbeda dengan hierarki FS. Hierarki adalah pengaturan file dan direktori di suatu OS, dan pengguna hanya melihat hierarki ini tanpa peduli FS yang dipakai.

## 4. Pengertian Distributed File System

DFS bukan sekadar FS yang tersebar di lebih dari satu mesin. Secara konseptual DFS adalah abstraksi terkoordinasi dari sekumpulan media penyimpan yang menutupi heterogenitas, kegagalan, dan konkurensi, sehingga menghasilkan jaringan penyimpanan yang terintegrasi, andal, dan berkinerja tinggi. Seperti sistem terdistribusi lain, medianya banyak tetapi tampak sebagai satu bagi pengguna.

Perbedaan dengan istilah lain:

- DFS adalah abstraksi level software yang mengelola data dalam file dan direktori yang bisa didistribusikan ke banyak node.
- SAN (Storage Area Network) menyajikan penyimpanan sebagai kumpulan block-level device yang bisa dihubungkan langsung oleh server dan diformat dengan FS sendiri. Secara konsep SAN bisa menjadi storage untuk DFS meskipun tidak disarankan. Penggunaan umumnya untuk backend database besar, editing video dan audio, serta virtualisasi skala besar.
- DFS lebih mengarah ke DAS (Direct-Attached Storage) di setiap node.

## 5. Berbagai DFS yang Umum

- Google File System
- Colossus, pengganti Google File System, dipakai di YouTube, Gmail, dan Google Docs
- CephFS
- Gluster
- HDFS (Hadoop Distributed File System)
- Lustre

## 6. Studi Kasus: HDFS

HDFS adalah file system open source yang scalable, terdistribusi, dan fault-tolerant, dirancang untuk mengelola dan menyimpan dataset besar di klaster commodity hardware. Commodity hardware adalah perangkat keras yang diproduksi massal, murah, dan mudah dipertukarkan dari berbagai vendor.

HDFS adalah komponen utama Apache Hadoop dan dipakai untuk pemrosesan data skala besar. File dibagi menjadi blok-blok, didistribusikan ke beberapa server, dan direplikasi untuk mencapai high availability. Menurut slide, Apache Hadoop 3.5.0 dikompilasi dengan JDK 17 dan bisa dijalankan dengan Java 17 (server) atau Java 17 dan 21 (klien).

Untuk memahami cara kerjanya, materi meminta praktik memakai HDFS sesuai dokumen panduan yang disertakan di EL (menggunakan-hadoop-hdfs-linux).

## 7. Cloud Storage

Cloud storage adalah layanan online yang memungkinkan pengguna menyimpan dan mengakses data di server jarak jauh lewat internet, bukan di hard drive lokal. Penyimpanan fisiknya mencakup beberapa server, kadang di beberapa lokasi, dan biasanya dimiliki dan dikelola penyedia cloud. Contoh: Amazon S3, Azure Blob Storage, dan Google Cloud Storage (GCS).

### 7.1 Relasi dengan DFS

Cloud storage adalah tempat penyimpanan data, sedangkan pengorganisasiannya dilakukan oleh DFS. Contohnya S3 bisa dipakai oleh HDFS: S3 sebagai cloud storage, sedangkan operasi file system dilakukan HDFS, dengan konfigurasi di core-site.xml.

### 7.2 Tipe cloud storage

- Object storage
- File storage
- Block storage

## 8. Praktik HDFS

Materi minggu ini dilengkapi petunjuk "Mulai Menggunakan Apache Hadoop - HDFS" (versi 27 April 2026) untuk memasang HDFS pada satu mesin Linux. Berikut ringkasan alurnya.

### 8.1 Persyaratan

- JDK 17 dan atau 21. JDK 17 dipakai untuk server, sedangkan klien bisa memakai JDK 17 atau 21.
- Apache Hadoop 3.5.0 (rilis 2 April 2026).
- pdsh, ssh, dan layanan sshd yang sudah berjalan.

### 8.2 Langkah pemasangan

1. **Unduh** distribusi Apache Hadoop dari situs resmi hadoop.apache.org.
2. **Instalasi Hadoop.** Ekstrak file tar.gz ke direktori pilihan, buat tautan simbolik ke `hadoop`, lalu atur variabel lingkungan PATH. Lokasi instalasi disebut `$HADOOP_HOME`.
3. **Instalasi pdsh**, yaitu alat untuk menjalankan perintah paralel lewat ssh. Setelah itu atur `PDSH_RCMD_TYPE=ssh` bila perlu.
4. **Atur ssh ke localhost tanpa password.** Buat kunci RSA tanpa passphrase, tambahkan kunci publik ke `authorized_keys`, dan beri izin file 0600.
5. **Konfigurasi Hadoop** pada empat file di `$HADOOP_HOME/etc/hadoop/`:
   - `hdfs-site.xml`: `dfs.replication` bernilai 1 karena hanya satu node.
   - `core-site.xml`: `fs.defaultFS` bernilai `hdfs://localhost:9000`, ditambah pengaturan proxy user.
   - `mapred-site.xml`: `mapreduce.framework.name` bernilai `yarn`.
   - `yarn-site.xml`: pengaturan layanan shuffle, whitelist environment, dan batas memori YARN.
6. **Format NameNode.** Dilakukan sekali saja. Jika diulang, berbagai file yang sudah diproses di HDFS akan terhapus.
7. **Atur JAVA_HOME** di `hadoop-env.sh` sesuai lokasi JDK di komputer.
8. **Jalankan daemon HDFS.** Status bisa dipantau lewat antarmuka web.

### 8.3 Setelah berhasil

Jika semua langkah berhasil, Hadoop dan HDFS siap dipakai. Panduan juga memuat contoh operasi file pada HDFS dan cara menghentikan HDFS. Daftar lengkap perintah ada di dokumentasi FileSystemShell Apache Hadoop.

### 8.4 Catatan

- Nilai replikasi 1 hanya cocok untuk praktik pada satu mesin. Pada klaster sungguhan nilainya lebih besar supaya data tetap aman jika satu node gagal, sesuai konsep replikasi di minggu 4.
- Konfigurasi `core-site.xml` juga yang dipakai untuk menghubungkan HDFS dengan cloud storage seperti S3.

## 9. Pandangan Pribadi

Hal yang paling jelas bagi saya dari materi ini adalah bahwa pengguna cukup melihat satu penyimpanan, padahal di belakangnya banyak mesin yang bekerja sama. Ini mirip dengan konsep transparansi di minggu 1. Saya juga melihat bahwa HDFS menutup kelemahan hardware murah dengan replikasi, sehingga klaster bisa dibangun dari mesin biasa tanpa takut satu disk rusak membuat data hilang.
