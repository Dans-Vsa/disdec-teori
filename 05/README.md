Materi Minggu 5: Fault Tolerance

AI yang digunakan: Claude (Anthropic)

Prompt: "Saya butuh bantuan untuk mencari sumber dan menyusun rangkuman materi minggu 5 dari slide kuliah."

Bagian yang disesuaikan dari keluaran prompt ada pada: saya memeriksa isi rangkuman terhadap slide kuliah, merapikan susunan dan kalimat, serta menulis bagian "Pandangan Pribadi" dengan pendapat saya sendiri.

---

# Rangkuman Minggu 5: Fault Tolerance

## 1. Tujuan Pembelajaran

1. Menjelaskan mengapa fault tolerance diperlukan pada sistem terdistribusi dan terdesentralisasi.
2. Menjelaskan pengertian fault tolerance dan fault tolerant system.
3. Menjelaskan jenis fault dan klasifikasinya berdasarkan sumber.
4. Memahami design pattern untuk mencapai fault tolerance dan menerapkannya dengan Python.
5. Memahami bagaimana berbagai bahasa pemrograman dan peranti pengembangan mengimplementasikan fault tolerance.

## 2. Pengertian Fault Tolerance

Fault tolerance adalah kemampuan sistem mempertahankan operasinya dengan tepat meskipun satu atau lebih komponennya mengalami kegagalan atau kesalahan. Kemampuan ini penting untuk sistem high availability, mission critical, maupun life-critical.

## 3. Arti Penting

Sistem terdistribusi dan terdesentralisasi punya banyak komponen, dan koneksi antar komponen bisa jadi masalah. Semakin kritis sebuah aplikasi, semakin tinggi tuntutan agar tetap berfungsi. Karena itu tuntutan high availability harus diantisipasi.

## 4. Jenis Fault

Tabel 1. Jenis fault

| Jenis | Penjelasan |
| --- | --- |
| Transient fault | sementara, misalnya karena petir. |
| Intermittent fault | muncul dan hilang berulang, misalnya karena hardware overheat. |
| Permanent fault | bersifat tetap. |
| Byzantine fault | komponen memberi hasil acak atau tidak konsisten. |

## 5. Klasifikasi Fault Berdasarkan Sumber

Tabel 2. Klasifikasi fault berdasarkan sumber

| Sumber | Contoh |
| --- | --- |
| Hardware fault | disk crash, power failure. |
| Software fault | bug, memory leak, race condition. |
| Communication fault | packet loss, network partition. |

## 6. Teknik untuk Mencapai Fault Tolerance

### 6.1 Redundansi dan replikasi

Resource tidak hanya satu, tetapi direplikasi ke berbagai node (redundansi) supaya tidak ada single point of failure. Lihat materi minggu 4.

### 6.2 Load balancing

Dipakai untuk men-scale aplikasi menjadi lebih dari satu instance (satu instance adalah satu proses aplikasi penuh yang aktif). Dilakukan dengan load balancer seperti HAProxy atau nginx ke beberapa instance aplikasi. Pembahasan rincinya ada di dokumen web-app-py-blacksheep-load-balancing-nginx.pdf.

### 6.3 Checkpointing dan rollback recovery

Checkpointing adalah menyimpan status sistem secara berkala ke media penyimpan. Rollback recovery adalah mengembalikan sistem ke checkpoint terakhir setelah terjadi kegagalan. Dengan keduanya sistem bisa pulih dari status tersimpan, bukan dari awal, sehingga kehilangan data dan waktu pemrosesan lebih sedikit.

### 6.4 Heartbeat dan failure detection

Heartbeat adalah sinyal periodik antar komponen yang menunjukkan komponen masih aktif. Failure detection memakai heartbeat itu untuk menentukan apakah komponen sudah gagal. Jika pemantau tidak menerima heartbeat dalam jangka waktu tertentu, komponen dianggap gagal. Pola ini penting agar kegagalan terdeteksi dan ditangani tepat waktu. Pada materi ada contoh script check-server.py untuk pengecekan komponen sederhana.

### 6.5 Retry

Library tenacity di Python bisa dipakai untuk mengelola proses yang bergantung pada komponen di luar aplikasi, karena koneksi ke luar selalu mungkin gagal. Contohnya ada di file check-retry.py.

### 6.6 Circuit breaker

Pola desain pada microservices untuk mencegah kegagalan berantai dengan memblokir sementara panggilan ke layanan yang gagal. Cara kerjanya seperti state machine dengan tiga status:

- **Closed**: permintaan diizinkan dan dipantau kegagalannya.
- **Open**: setelah ambang kegagalan tercapai, permintaan berikutnya langsung ditolak agar layanan punya waktu pulih.
- **Half-open**: setelah batas waktu, sejumlah kecil permintaan diizinkan untuk menguji apakah layanan sudah pulih. Jika berhasil kembali ke closed, jika gagal kembali ke open.

### 6.7 Consensus dan coordination

Consensus adalah proses beberapa node menyepakati satu nilai, status, atau urutan operasi walau ada kegagalan dan penundaan jaringan. Coordination adalah pengelolaan interaksi antar node yang lebih luas, dan sering memakai consensus sebagai komponen kunci.

Contoh:

- Pemilihan leader pada cluster database dengan replikasi, memakai algoritma seperti Raft.
- Update file di cloud storage seperti Google Drive, yang dianggap final saat mayoritas server menyetujui.
- Cryptocurrency dan blockchain, yang memakai consensus untuk validasi transaksi.

## 7. Actor Model untuk Supervisi Proses

Pada actor model, aplikasi dibangun dari actor yang saling berkirim pesan dan tidak berbagi state. Supervisor mengawasi worker dan bisa memulai ulang worker yang gagal.

## 8. Implementasi pada Berbagai Bahasa

- Erlang dan Elixir punya mekanisme bawaan untuk actor model dan supervisor-worker.
- Pony adalah bahasa pemrograman yang khusus berfokus pada actor model.
- Bahasa lain biasanya memakai library pihak ketiga. Di Python ada Xoscar, tenacity, dan circuit breaker. Di Java ada Apache Pekko untuk actor model dan supervisor-worker, serta Resilience4j untuk fault tolerance.
- Library serupa bisa dicari di ekosistem lain, misalnya npm untuk JavaScript dan crates.io untuk Rust.

## 9. Pandangan Pribadi

Menurut saya fault tolerance bukan berarti sistem tidak pernah gagal, tetapi sistem tetap berjalan ketika bagian dari dirinya gagal. Dari semua teknik di atas, yang paling mudah diterapkan lebih dulu adalah retry dan heartbeat, karena sederhana dan langsung berguna. Circuit breaker menarik karena mencegah satu layanan yang bermasalah menjatuhkan layanan lain.
