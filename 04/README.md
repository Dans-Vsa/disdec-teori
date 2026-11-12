Materi Minggu 4: Konsistensi dan Replikasi pada Sistem Terdistribusi

AI yang digunakan: Claude (Anthropic)

Prompt: "Saya butuh bantuan untuk mencari sumber dan menyusun rangkuman materi minggu 4 dari slide kuliah."

Bagian yang disesuaikan dari keluaran prompt ada pada: saya memeriksa isi rangkuman terhadap slide kuliah, merapikan susunan dan kalimat, serta menulis bagian "Pandangan Pribadi" dengan pendapat saya sendiri.

---

# Rangkuman Minggu 4: Konsistensi dan Replikasi

## 1. Tujuan Pembelajaran

1. Menjelaskan konsep konsistensi pada sistem terdistribusi.
2. Menjelaskan berbagai model konsistensi.
3. Menjelaskan konsep replikasi data.
4. Menjelaskan protokol dan strategi replikasi.
5. Menganalisis studi kasus replikasi pada sistem nyata di industri.

## 2. Konsistensi pada Sistem Terdistribusi

Konsistensi menjamin semua node memiliki data yang sama, atau tampak sama, pada waktu tertentu, walau data berada di banyak tempat dan bisa diubah bersamaan. Jika data diubah di satu server, sistem harus memastikan server lain akhirnya punya data yang sama.

Kenapa penting? Tanpa konsistensi, pengguna bisa melihat versi data berbeda tergantung server tempat dia membaca. Contohnya transfer uang dari rekening A ke B: server 1 sudah mengurangi saldo A tetapi server 2 belum menambah saldo B, sehingga nasabah tidak bisa memakai saldo B untuk transaksi. Itu berarti sistem tidak konsisten.

## 3. Teorema CAP

Partisi jaringan adalah hal yang wajar dan hampir pasti terjadi, sehingga kondisi ideal CAP tidak mungkin terpenuhi seluruhnya.

- **Consistency**: semua node punya data yang sama.
- **Availability**: data tersedia saat dibutuhkan pengguna.
- **Partition Tolerance**: sistem tetap berjalan walau ada komponen jaringan yang gagal atau terputus.

Contoh e-commerce dengan Server A untuk wilayah barat Indonesia dan Server B untuk wilayah timur. Jika jaringan antar keduanya terputus:

- **Memilih availability (AP)**: pengguna tetap bisa belanja di kedua wilayah, tetapi stok bisa sementara tidak sinkron.
- **Memilih consistency (CP)**: sistem menolak transaksi sampai jaringan pulih agar stok tetap konsisten, artinya availability dikorbankan.

## 4. Model Konsistensi

Model konsistensi adalah aturan tentang bagaimana dan kapan perubahan data terlihat oleh node atau klien lain.

1. **Strong consistency**. Setelah data diperbarui, semua node langsung melihat versi terbaru. Biasanya memakai Two-Phase Commit atau Paxos. Contoh: sistem perbankan.
2. **Eventual consistency**. Data tidak langsung sama di semua node tetapi akhirnya akan sama jika tidak ada update baru. Cocok untuk sistem yang mengutamakan availability dan kecepatan. Contoh: Amazon DynamoDB dan Apache Cassandra. Dikenal dengan istilah BASE: Basically Available (tetap bisa dibaca atau ditulis walau ada kegagalan sebagian), Soft state (replika tidak harus konsisten sepanjang waktu), dan Eventual consistency.
3. **Causal consistency**. Jika satu operasi bergantung pada operasi lain, urutannya dijaga. Contoh: komentar tidak boleh muncul sebelum postingannya.
4. **Read-your-writes consistency**. Setelah pengguna menulis data, pembacaan berikutnya oleh pengguna itu melihat hasil tulisannya. Contoh: pesan yang baru dikirim langsung muncul di layar pengirim.
5. **Monotonic read consistency**. Pengguna tidak akan melihat versi yang lebih lama dari yang sudah pernah dilihat. Contoh: timeline tidak mundur setelah di-refresh.
6. **Sequential consistency**. Semua operasi tampak dieksekusi dalam urutan yang sama untuk semua node walau tidak harus real-time. Contoh: semua pengguna melihat urutan posting yang sama walau ada sedikit delay.
7. **Linearizability**. Varian paling kuat dari strong consistency. Operasi tampak atomik dan langsung dengan urutan yang sama bagi semua node, seakan-akan sistem hanya satu node. Dipakai di etcd, ZooKeeper, dan Spanner. Contoh: memastikan dua pengguna tidak bisa mendaftar dengan username yang sama bersamaan.

## 5. Trade-off Latensi dan Konsistensi

Semakin tinggi konsistensi yang dijamin, biasanya semakin besar latensi yang ditanggung pengguna.

- **Strong consistency**: sistem menunggu semua node sinkron sebelum merespons. Datanya pasti sama tetapi latensi tinggi karena harus komunikasi lintas jaringan.
- **Eventual consistency**: sistem langsung merespons tanpa menunggu. Latensi rendah tetapi data bisa belum terbaru.

Contoh dua pusat data di Jakarta dan Yogyakarta. Jika pengguna mengubah data di Jakarta, strong consistency menunggu Yogyakarta sama persis sebelum menjawab, sedangkan eventual consistency menjawab langsung dan mengirim update ke Yogyakarta belakangan.

## 6. Replikasi Data

Replikasi adalah membuat dan memelihara salinan data yang sama di beberapa node atau server. Contohnya database utama di Jakarta dengan salinan di Yogyakarta dan Denpasar.

Tujuannya:

- **Ketersediaan**: jika satu server gagal, yang lain masih punya salinan.
- **Kinerja**: pengguna membaca dari server terdekat.
- **Reliabilitas**: risiko kehilangan data turun karena ada cadangan di banyak tempat.
- **Skalabilitas**: beban baca dibagi ke beberapa server.

## 7. Jenis Replikasi

Tabel 1. Jenis replikasi

| Jenis | Penjelasan |
| --- | --- |
| Master-slave (primary-secondary) | satu master menulis, slave hanya membaca. Konsistensinya kuat tetapi master bisa jadi bottleneck. Contoh: PostgreSQL streaming replication. |
| Multi-master (peer-to-peer) | semua node bisa menulis dan membaca. Fleksibel tetapi rawan konflik data. Contoh: PostgreSQL logical replication, Apache Cassandra, MongoDB, Apache Ignite. |
| Synchronous | update ditulis ke semua node sebelum dianggap selesai. Konsisten tetapi lambat. Contoh: Google Spanner. |
| Asynchronous | update dikirim ke node lain setelah transaksi selesai di master. Cepat tetapi bisa ada delay data. Contoh: MySQL mode async dan DynamoDB. |

## 8. Strategi Sinkronisasi Replika

Berdasarkan waktu sinkronisasi:

Tabel 2. Waktu sinkronisasi

| Mode | Penjelasan |
| --- | --- |
| Asynchronous | sumber commit tanpa menunggu replika. Tercepat tetapi risiko kehilangan data tertinggi. |
| Semi-synchronous | sumber menunggu satu replika menerima data. Seimbang antara konsistensi dan performa, dan ini yang dipakai MySQL. |
| Synchronous | sumber menunggu semua replika commit. Konsistensi tertinggi tetapi performa terendah. |

Berdasarkan arah replikasi: master-slave, master-master, dan master-master asymmetric (peran dan kemampuan node berbeda).

Berdasarkan cara sinkronisasi:

- **Pull-based**: replika memeriksa update secara periodik di sumber.
- **Push-based**: sumber aktif mengirim update ke replika saat terjadi perubahan, memakai CDC (Change Data Capture) dan pengiriman lewat WebSockets atau SSE.
- **Hybrid**: campuran keduanya.

Penanganan konflik jika terjadi banyak update bersamaan:

- **LWW (Last Write Wins)**: yang timestamp-nya terbaru dipakai.
- **Vector clock**: pengurutan memakai vector clock.
- **CRDT (Conflict-free Replicated Data Type)**: memakai struktur data di tiap node, bukan di server pusat. Contohnya Yjs untuk editor kolaboratif seperti Google Docs.
- **OT (Operational Transformation)**: transformasi berdasarkan informasi lokal.

Contoh CRDT pada slide: dua orang menghitung pengunjung website. Satu mencatat 5 dan yang lain 3. Saat sinkronisasi dipakai aturan maksimum per orang, hasilnya 5 dan 3, dan totalnya dijumlahkan menjadi 8.

Contoh OT pada slide: dokumen "abc". Satu orang menyisipkan "x" di indeks 0 dan orang lain menghapus "c" di indeks 2. Tanpa transformasi, hasilnya bisa berbeda ("xac" dan "xab"). Dengan OT, perintah hapus indeks 2 diubah menjadi hapus indeks 3 setelah ada sisipan di depan, sehingga keduanya menghasilkan "xab".

## 9. Protokol Replikasi

1. **Primary-based (master-slave)**: satu node menulis dan membaca, lainnya hanya membaca.
2. **Quorum-based**: operasi dianggap berhasil jika dikonfirmasi sejumlah node tertentu.
3. State machine replication.
4. Gossip protocol.
5. Chain replication.

### 9.1 Quorum-based

Operasi tulis (write) dan baca (read) berhasil hanya jika mendapat konfirmasi dari sejumlah node. Syaratnya W + R > N, dengan W jumlah node minimal untuk tulis, R untuk baca, dan N total node.

Contoh N = 3 (node A, B, C), W = 2, R = 2, dan 2 + 2 > 3 terpenuhi. Klien menulis x = 100 ke ketiga node, A dan B membalas ACK sedangkan C lambat. Karena sudah ada 2 ACK, tulis dinyatakan berhasil. Saat membaca dari minimal 2 node, misalnya A dan C, versi terbaru dari A pasti ikut terbaca karena adanya irisan.

### 9.2 State machine replication

Beberapa replika mengeksekusi perintah yang sama dalam urutan yang sama. Contoh: replika A, B, C punya x = 10. Klien mengirim SET x = 20 ke leader (A). A memberi nomor urut, mencatat di log, dan mengirim ke B dan C. Setelah B dan C mengonfirmasi, A menganggap perintah "dikomit". Semua replika lalu mengeksekusi SET x = 20, dan leader mengirim hasil ke klien.

### 9.3 Gossip protocol

Dipakai di database terdistribusi seperti Apache Cassandra untuk memeriksa keberadaan node dan replikasi data. Setiap detik sebuah node memilih 1 sampai 3 peer secara acak dan membagikan statusnya. Peer yang menerima memperbarui statusnya lalu menyebarkannya ke peer lain, sehingga dalam beberapa putaran seluruh cluster tahu perubahan tersebut.

### 9.4 Chain replication

Replika disusun berantai. Penulisan masuk dari kepala rantai dan diteruskan sampai ekor, dan pembacaan dilayani oleh ekor.

## 10. Studi Kasus

Studi kasus pada materi adalah PostgreSQL 18 streaming replication dan Apache Ignite 3 master-master. Detailnya ada di dokumen lecture notes dan source code yang menyertai materi.

## 11. Pandangan Pribadi

Kelebihan strong consistency adalah datanya selalu benar, yang wajib untuk transaksi uang. Kekurangannya lambat dan bisa membuat layanan berhenti saat jaringan bermasalah. Eventual consistency kebalikannya: cepat dan tetap tersedia, tetapi pengguna bisa melihat data lama untuk sementara. Menurut saya pilihannya harus dilihat dari akibat kesalahannya. Kalau salah data berarti kehilangan uang, pakai strong. Kalau paling buruk cuma jumlah like yang telat, eventual sudah cukup.

Jika replikasi gagal di satu node, sistem seharusnya tetap melayani lewat node lain yang sehat, lalu node yang gagal dikejar ketertinggalannya (misalnya lewat log atau gossip) setelah pulih. Pada quorum, selama jumlah node yang tersisa masih memenuhi W dan R, operasi tetap berjalan.
