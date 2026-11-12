Materi Minggu 2: Proses dan Komunikasi Antar Proses pada Sistem Terdistribusi

AI yang digunakan: Claude (Anthropic)

Prompt: "Saya butuh bantuan sedikit untuk menyusun rangkuman materi minggu 2 dari slide, sekaligus mencari penjelasan tambahan untuk istilah yang kurang jelas."

Bagian yang disesuaikan dari keluaran prompt ada pada: saya membaca ulang rangkuman bersama slide, merapikan susunannya, dan menambahkan pandangan pribadi pada bagian diskusi.


---

# Rangkuman Minggu 2: Proses dan Komunikasi Antar Proses

## 1. Inti Materi

Proses di node yang berbeda tidak berbagi memori, sehingga harus berkomunikasi lewat message passing, RPC, atau middleware. Karena tiap node punya clock sendiri, urutan kejadian dijaga dengan sinkronisasi clock atau logical clock Lamport, sementara masalah seperti kehilangan pesan dan inkonsistensi ditangani dengan acknowledgement, retransmisi, dan konsensus.

## 2. Tujuan Pembelajaran

1. Menjelaskan konsep proses pada sistem terdistribusi.
2. Menjelaskan berbagai jenis komunikasi antar proses (IPC).
3. Menganalisis isu sinkronisasi, reliabilitas, dan keamanan.
4. Mengaitkan konsep dengan contoh nyata seperti microservices, cloud, dan distributed messaging.

## 3. Proses dan Manajemen Proses

Proses adalah program yang sedang dieksekusi. Manajemen proses mencakup:

- **Process creation**: pembuatan proses baru
- **Process migration**: perpindahan proses dari satu node ke node lain
- **Process monitoring**: pemantauan kondisi proses

## 4. Mengapa Perlu Komunikasi Antar Proses

Pada sistem terdistribusi, setiap node menjalankan proses sendiri, tetapi semuanya harus bekerja sebagai satu kesatuan logis. Karena itu antar node harus berkomunikasi. Proses bisa berada di node yang berbeda sehingga tidak ada shared memory di antara mereka, jadi dibutuhkan IPC khusus untuk sistem terdistribusi.

Contohnya:

1. Game multiplayer
2. Gojek/Grab: antarmuka pengguna berkomunikasi dengan layanan driver, pelacakan lokasi, dan penghitungan biaya
3. Chat dan video call WhatsApp, yaitu komunikasi point-to-point pada infrastruktur terdistribusi

## 5. Model Komunikasi Pesan

Tabel 1. Model komunikasi pesan

| Model | Penjelasan |
| --- | --- |
| Langsung | Menggunakan socket |
| Tidak langsung | Menggunakan mailbox atau message queue |
| Pub/Sub | Publisher menerbitkan pesan ke sebuah topik, lalu subscriber yang berlangganan topik itu menerimanya |

## 6. Jenis IPC

Ada empat jenis IPC: Shared Memory, Message Passing, Remote Procedure Call, dan Middleware. Shared memory tidak cocok untuk sistem terdistribusi karena tidak ada akses ke memori pada node-node yang berbeda.

### 6.1 Message Passing

- Standarnya MPI (Message Passing Interface). Versi terakhir menurut slide adalah 5.0.
- **Contoh implementasi di Python**: mpi4py.

### 6.2 RPC (Remote Procedure Call)

Memanggil prosedur di node lain seolah-olah memanggil fungsi lokal. Contohnya gRPC.

### 6.3 MOM (Message-Oriented Middleware)

Infrastruktur yang memfasilitasi komunikasi dan pertukaran data lewat pesan, yaitu unit informasi mandiri yang dikirim melalui saluran komunikasi. Pesan dikirim dan diterima secara asinkron.

Standar dan protokol MOM:

1. X/Open XATMI, spesifikasi distributed transaction processing
2. AMQP, biasanya untuk aplikasi enterprise
3. HLA (IEEE 1516) dari IEEE dan SISO
4. MQTT, biasanya untuk perangkat dengan sumber daya terbatas
5. DDS dari OMG (Object Management Group)
6. XMPP (eXtensible Messaging and Presence Protocol)

## 7. Studi Kasus: Microservices

Pada microservices, aplikasi dipecah menjadi layanan-layanan kecil yang berjalan sebagai proses terpisah dan saling berkomunikasi lewat jaringan memakai mekanisme IPC di atas.

## 8. Komunikasi Sinkron dan Asinkron

- **Sinkron**: pengirim menunggu respons sebelum melanjutkan pekerjaan.
- **Asinkron**: pengirim tidak menunggu, pesan diproses kemudian (misalnya lewat message queue).

## 9. Masalah pada Komunikasi Antar Proses

Tabel 2. Masalah pada komunikasi antar proses

| Masalah | Penjelasan dan solusi |
| --- | --- |
| Reliability | Pesan harus terkirim lengkap dan berurutan walau ada paket hilang atau crash. Solusinya acknowledgement, transmisi ulang, dan protokol andal seperti TCP. |
| Ordering | Pesan harus diproses dalam urutan yang logis. Solusinya pipes (FIFO), message queue, shared memory, socket dan message passing, serta RPC. |
| Security | Solusinya enkripsi dan autentikasi. |
| Performance | Dipengaruhi latency dan bandwidth. |

## 10. Protokol Komunikasi

- **Protokol dasar**: TCP, UDP, HTTP.
- Protokol tingkat atas yang berjalan di atas HTTP: gRPC, GraphQL, REST.

## 11. Sinkronisasi Clock

Setiap node punya clock lokal sendiri. Karena latensi jaringan dan pergeseran (drift) perangkat keras, clock lokal lama-lama menyimpang. Sinkronisasi clock menyelaraskan clock beberapa komputer ke waktu yang sama dan konsisten, penting untuk pengurutan kejadian yang akurat, konsistensi data, dan operasi yang terkoordinasi.

Metode umum:

- Sinkronisasi eksternal memakai atomic clock (International Atomic Time / TAI), GPS, dan UTC.
- Protokol NTP (Network Time Protocol) atau PTP (Precision Time Protocol). PTP lebih presisi daripada NTP.

## 12. Logical Clock (Lamport)

Kalau pencatatan memakai timestamp jam sungguhan, urutan kejadian bisa bergeser karena latensi atau gangguan jaringan. Lamport menawarkan logical clock yang tidak terkait dengan waktu nyata. Aturannya:

1. Setiap ada kejadian, angka logical clock bertambah satu.
2. Saat mengirim pesan, node menyertakan angka logical clock-nya.
3. Saat menerima pesan, node mengambil angka terbesar antara clock lokalnya dan angka di pesan, lalu menambah 1.

Contoh di slide, semua service mulai dari 0:

1. Sales Service mencatat transaksi, clock menjadi 1, lalu mengirim ke Product Service dengan angka 1.
2. Product Service menerima pesan berangka 1. Clock lokalnya 0, jadi diambil yang terbesar (1) lalu ditambah 1 menjadi 2. Setelah itu mengirim ke Invoice Service dengan angka 2.
3. Invoice Service menerima pesan berangka 2. Clock lokalnya 0, diambil yang terbesar (2) lalu ditambah 1 menjadi 3, kemudian membuat faktur.

Dengan begini urutan yang benar tetap terjaga: Sales, Product, Invoice.

Lamport clock masih berupa scalar clock. Pengembangan berikutnya antara lain Vector Clocks (Fidge dan Mattern, 1988), Plausible Clocks (1999), Chain Clocks (2005), Interval Tree Clocks (2008), dan Blum Clocks (2019).

## 13. Koordinasi Proses

Koordinasi proses adalah mekanisme yang membuat beberapa proses independen di berbagai komputer bisa bekerja sama dengan mengelola komunikasi, sinkronisasi, dan akses ke sumber daya bersama tanpa konflik seperti deadlock atau race condition. Contoh perangkatnya: Apache ZooKeeper, etcd, Consul, ClickHouse Keeper, Oxia, dan KRaft.

## 14. Tantangan dan Solusi

Tabel 3. Tantangan dan solusi

| Tantangan | Solusi |
| --- | --- |
| Message loss | Retransmission dan acknowledgement |
| Deadlock | Timeout dan algoritma deteksi |
| Data inconsistency | Protokol konsensus (Raft, Paxos) |
| Security | SSL/TLS dan OAuth2 |

## 15. Isu Terkini

- Edge dan Fog Computing
- Observability dan tracing (telemetry)
- **Orkestrasi container**: Kubernetes dan Docker Swarm

## 16. Pandangan Pribadi (Diskusi)

Apakah GraphQL termasuk RPC?

Menurut saya sebagian. GraphQL memang memanggil operasi di server dari jarak jauh, tetapi bedanya klien menentukan sendiri bentuk dan isi data yang diminta, sehingga lebih dekat ke bahasa query daripada pemanggilan fungsi tetap seperti pada gRPC.

Apakah REST termasuk RPC?

Menurut saya tidak. REST berorientasi pada sumber daya (resource) yang diakses lewat URL dan metode HTTP, bukan pada pemanggilan prosedur. Tetapi dalam praktiknya banyak API yang disebut REST sebenarnya bergaya RPC, misalnya endpoint seperti /hitungBiaya.

Apakah telemetry solusi yang etis?

Menurut saya tergantung penerapannya. Telemetry sangat membantu untuk memantau dan memperbaiki sistem, tetapi jadi tidak etis kalau mengumpulkan data pribadi tanpa sepengetahuan dan persetujuan pengguna. Telemetry yang etis harus transparan, mengumpulkan data seperlunya, dan memberi pilihan untuk menolak.
