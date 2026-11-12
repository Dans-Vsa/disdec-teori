Materi Minggu 3: Sinkronisasi dan Koordinasi pada Sistem Terdistribusi

AI yang digunakan: Claude (Anthropic)

Prompt: "Saya butuh bantuan untuk mencari sumber dan menyusun rangkuman materi minggu 3 dari slide kuliah."

Bagian yang disesuaikan dari keluaran prompt ada pada: saya memeriksa isi rangkuman terhadap slide kuliah, merapikan susunan dan kalimat, serta menulis bagian "Pandangan Pribadi" dengan pendapat saya sendiri.

# Rangkuman Minggu 3: Sinkronisasi dan Koordinasi

## Tujuan Pembelajaran

1. Menjelaskan mengapa sinkronisasi diperlukan untuk koordinasi pada sistem terdistribusi.
2. Menjelaskan tantangan sinkronisasi.
3. Menjelaskan sinkronisasi waktu fisik beserta algoritma dan protokolnya.
4. Menjelaskan sinkronisasi waktu logis beserta algoritmanya.
5. Menjelaskan masalah mutual exclusion dan algoritma penyelesaiannya.
6. Menjelaskan masalah pemilihan koordinator dan cara memecahkannya.
7. Memberi contoh studi kasus nyata di industri.

## Apa itu Sinkronisasi

Sinkronisasi adalah koordinasi proses, data, dan sumber daya di berbagai node supaya operasi tetap konsisten dan andal. Sinkronisasi membantu menjaga konsistensi data, mengelola proses konkuren, dan membuat sistem berperilaku koheren di seluruh node. Dengan mekanisme yang tepat, konflik data (data race) bisa dicegah.

Contoh race condition pada e-commerce:

- Stok produk X ada 10.
- User A dan user B sama-sama ingin membeli 1 produk X.
- A membaca stok, hasilnya 10, aman untuk lanjut.
- B juga membaca stok, hasilnya 10, aman untuk lanjut.
- A membeli dan mengurangi stok menjadi 9.
- B membeli dan juga mengurangi stok menjadi 9.
- Kenyataannya ada 2 pembelian sehingga stok seharusnya 8, tetapi yang tercatat 9.

## Mengapa Sinkronisasi Penting

- Integritas data, supaya tidak terjadi data race maupun race condition.
- Sinkronisasi state di semua node. Contohnya game multiplayer yang mengirim posisi, kesehatan, dan inventaris pemain ke semua klien, atau keranjang belanja yang sama di browser dan basis data.
- Koordinasi tugas dan pengelolaan sumber daya.
- Mekanisme recovery dengan menjaga state.
- Penggunaan yang efisien karena operasi redundan dicegah.
- Load balancing dan operasi yang scalable.
- Mencegah deadlock.

## Tantangan Sinkronisasi

- Network latency dan partitioning
- Skalabilitas
- Sistem yang fault-tolerant
- Concurrency control
- Konsistensi data
- Sinkronisasi waktu

## Sinkronisasi Waktu Fisik

Sinkronisasi waktu fisik adalah penyelarasan real-time clock di sistem terdistribusi dengan standar waktu umum. Hal ini penting untuk korelasi log, penjadwalan event, dan menjaga konsistensi sistem. Caranya lewat protokol dan atau sinkronisasi dengan sumber berbasis perangkat keras, yaitu atomic clock dan GPS.

Standar waktu:

- GMT (Greenwich Mean Time) sudah tidak digunakan.
- UTC (Universal Coordinated Time) didefinisikan oleh ITU dan dipakai sebagai standar sejak 1963. UTC berbasis pada TAI (International Atomic Time).
- LTC (Coordinated Lunar Time) masih dalam pengembangan.

Alur sinkronisasinya: master atomic clock, atomic clock di satelit GPS, GPS dongle, server NTP atau PTP, lalu klien (OS dan ponsel).

Sumber atomic clock saat ini:

- Satelit GPS yang dioperasikan U.S. Space Force, sekitar 31 satelit di Medium Earth Orbit.
- Deep Space Atomic Clock (DSAC) milik NASA yang diluncurkan 2019.
- Misi Atomic Clock Ensemble in Space (ACES) di International Space Station.
- Ground control untuk master clock di U.S. Naval Observatory dan Schriever Space Force Base.

Protokol:

- NTP (Network Time Protocol).
- PTP (Precision Time Protocol), standar IEEE 1588. Versi 2008 tidak backward compatible dengan versi 2002, dan versi 2019 menambahkan perbaikan yang kompatibel dengan 2008. IEEE 802.1AS (gPTP) adalah adaptasinya untuk Audio Video Bridging dan Time-Sensitive Networking. Syarat lain: NIC harus mendukung hardware timestamping.

## Sinkronisasi Waktu Logis

Sinkronisasi waktu logis menetapkan urutan sebab akibat dari kejadian (causal order) dan tidak terkait dengan waktu fisik. Jika A terjadi sebelum B dan B sebelum C, maka A terjadi sebelum C (transitivitas). Ini berasal dari Leslie Lamport (lihat minggu 2).

Vector Clock:

Setiap proses memiliki vektor dengan satu posisi untuk tiap proses. Untuk tiga proses A, B, C, posisinya A di indeks 0, B di indeks 1, C di indeks 2, dan semuanya mulai dari [0,0,0].

Aturannya: setiap ada event (internal, kirim, atau terima), proses menambah 1 pada posisinya sendiri. Saat menerima pesan, proses mengambil nilai maksimum per elemen dari vektornya dan vektor pesan, lalu menambah posisinya sendiri.

Contoh pada slide:

- A melakukan internal event menjadi [1,0,0], lalu menambah lagi sebelum mengirim ke B, sehingga mengirim [2,0,0].
- B menerima, mengambil maksimum per elemen sehingga [2,0,0], lalu menambah posisinya dan mengirim [2,1,0] ke C.
- C menerima, mengambil maksimum, lalu menambah posisinya menjadi [2,1,1] dan setelah satu internal event lagi menjadi [2,1,2].
- Posisi akhir: A [2,0,0], B [2,1,0], C [2,1,2].

Aturan perbandingan:

- A terjadi sebelum B jika semua angka pada clock A lebih kecil atau sama dengan clock B dan minimal satu lebih kecil.
- Dua event concurrent (independen) jika tidak ada yang lebih kecil atau sama di semua komponen. Contohnya C [2,1,2] dan A [2,0,0] di akhir adalah independen menurut slide.

## Mutual Exclusion

Mutual exclusion memastikan hanya satu proses atau thread yang mengakses sumber daya bersama (printer, file, database) pada satu waktu. Bagian kode yang mengakses sumber daya bersama disebut critical section (CS). Kalau lebih dari satu proses masuk bersamaan, data bisa tidak konsisten atau rusak.

Prinsipnya:

- Mutual exclusion: hanya satu proses yang bekerja pada sumber daya bersama.
- Progress: jika tidak ada yang sedang di CS, sumber daya bisa diakses.
- Bounded waiting: tidak ada proses yang menunggu selamanya.
- Fairness: permintaan dilayani sesuai urutan yang wajar.

Pendekatan penyelesaiannya:

1. Centralized algorithm. Satu node menjadi koordinator. Proses meminta izin, koordinator memberi akses jika CS bebas, dan saat dilepas koordinator mengizinkan yang berikutnya. Kelebihannya sederhana dan murah. Kekurangannya single point of failure dan bisa jadi bottleneck.
2. Ricart-Agrawala. Terdistribusi penuh tanpa koordinator. Setiap proses mengirim permintaan dengan stempel waktu, dan boleh masuk CS jika semua proses lain menjawab OK. Setiap CS butuh 2 x (N - 1) pesan. Kelebihannya fault tolerant, kekurangannya overhead pesan tinggi.
3. Token-based. Sebuah token unik beredar di antara proses, dan hanya pemegang token yang boleh masuk CS. Kelebihannya hanya 1 pesan per entri CS. Kekurangannya kehilangan token sulit dideteksi dan butuh mekanisme pemulihan.

## Pemilihan Koordinator

Beberapa node mungkin butuh satu koordinator untuk mengelola sumber daya bersama, menyinkronkan operasi, dan mengambil keputusan global. Ketika koordinator gagal atau ada node baru bergabung, sistem harus memilih pemimpin baru secara otomatis.

Algoritma yang bisa dipakai: Bully, Ring, serta Raft atau Paxos (berbasis konsensus). Contohnya Apache ZooKeeper memakai Zab (ZooKeeper Atomic Broadcast, mirip Paxos), dan Kubernetes memakai Raft lewat etcd.

## Pandangan Pribadi

Menurut saya sinkronisasi adalah masalah yang paling mudah diremehkan. Contoh stok e-commerce di atas terlihat sepele, tetapi kalau tidak ditangani, penjual bisa menjual barang yang sebenarnya sudah habis. Dari semua pendekatan mutual exclusion, saya melihat tidak ada yang sempurna. Yang terpusat sederhana tetapi punya titik gagal tunggal, sedangkan yang terdistribusi lebih tahan gagal tetapi lebih banyak pesan. Jadi pilihannya bergantung pada kebutuhan sistem.
