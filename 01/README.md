Materi Minggu 1: Pengenalan Sistem Terdistribusi dan Terdesentralisasi

AI yang digunakan: Claude (Anthropic)

Prompt: "Tolong bantu saya mencari sumber dan menyusun rangkuman materi minggu 1 dari slide kuliah."

Bagian yang disesuaikan dari keluaran prompt ada pada: saya mencocokkan isi dengan slide kuliah dan menyesuaikan beberapa kalimat agar lebih mudah saya pahami. Bagian "Pandangan Pribadi" saya tulis berdasarkan pendapat saya sendiri.


---

# Rangkuman Minggu 1: Pengenalan Sistem Terdistribusi dan Terdesentralisasi

## 1. Inti Materi

Sistem terdistribusi membagi beban kerja ke banyak komputer yang tampil sebagai satu sistem, sedangkan sistem terdesentralisasi membagi kontrol sehingga tidak ada satu titik pengendali. Keduanya dibutuhkan untuk skalabilitas dan fault tolerance, dengan CAP theorem sebagai batasan utama yang harus dipertimbangkan perancang sistem.

## 2. Tujuan Pembelajaran

1. Memahami konsep sistem terdistribusi dan sistem terdesentralisasi.
2. Memahami perbedaan antara keduanya.
3. Mengenali contoh penerapannya di dunia nyata.
4. Mengetahui tantangan dan tren terbaru di bidang ini.

## 3. Arti Penting

Layanan modern seperti Google, Netflix, dan blockchain tidak mungkin berjalan pada satu komputer saja. Jumlah data dan pengguna yang terus bertambah menuntut sistem yang bisa berkembang mengikuti beban, tetap berjalan ketika sebagian komponennya gagal, dan tersedia 24 jam.

- **Skalabilitas**: kemampuan sistem menangani peningkatan beban kerja, pengguna, atau permintaan tanpa menurunkan kinerja.
- **Fault-tolerant**: kemampuan sistem tetap beroperasi normal meskipun sebagian komponen (hardware maupun software) gagal.

## 4. Sistem Terdistribusi

Menurut Tanenbaum dan van Steen (2007), sistem terdistribusi adalah kumpulan komputer independen yang tampak bagi penggunanya sebagai satu sistem yang koheren. Komponennya independen, berkomunikasi lewat jaringan, dan menyediakan transparansi. Transparansi artinya pengguna tidak perlu tahu lokasi data, cara aksesnya, proses replikasi, atau kegagalan yang terjadi pada salah satu node.

### 4.1 Arsitektur dasar

Tabel 1. Arsitektur dasar sistem terdistribusi

| Arsitektur | Penjelasan |
| --- | --- |
| Client-Server | client mengirim permintaan dan server melayaninya. Peran keduanya terpisah jelas. |
| Peer-to-Peer (P2P) | setiap node setara dan bisa jadi client sekaligus server. |
| Hybrid | gabungan keduanya, misalnya server pusat untuk pencarian dan P2P untuk pengiriman data. |

### 4.2 Karakteristik

1. Transparansi lokasi
2. Skalabilitas
3. Fault tolerance
4. Kinerja tinggi
5. Heterogenitas (perangkat keras dan lunaknya beragam)

## 5. Sistem Terdesentralisasi

Pada sistem tersentralisasi, kontrol ada di satu titik. Pada sistem terdesentralisasi, kontrol dibagi sehingga tidak ada single point of failure, dan keputusan diambil bersama lewat konsensus. Contohnya Blockchain, DNS, dan BitTorrent.

## 6. Perbedaan Terdistribusi dan Terdesentralisasi

Tabel 2. Perbandingan kedua jenis sistem

| Aspek | Terdistribusi | Terdesentralisasi |
| --- | --- | --- |
| Fokus | Pembagian beban kerja | Pembagian kontrol |
| Contoh | Google File System, MinIO Object Storage | Blockchain |

Keduanya berkaitan tetapi fokusnya berbeda. Sistem terdesentralisasi biasanya juga terdistribusi, sedangkan sistem terdistribusi belum tentu terdesentralisasi karena kontrolnya masih bisa dipegang satu organisasi.

## 7. Studi Kasus

Terdistribusi:

- Google File System dan HDFS untuk penyimpanan data besar
- Apache Kafka dan Apache Pulsar untuk messaging dan streaming
- Netflix, Twitch, dan YouTube
- Instagram, Threads, Facebook, dan TikTok

Terdesentralisasi:

- Blockchain (Bitcoin, Ethereum, Algorand, Sui, Near, Aptos, dan lainnya)
- DNS
- BitTorrent

## 8. CAP Theorem

CAP Theorem dari Eric Brewer (2000) menyatakan bahwa pada penyimpanan data terdistribusi ada tiga properti:

- **Consistency**: setiap operasi baca mengembalikan hasil penulisan terbaru, atau error.
- **Availability**: setiap permintaan ke node yang tidak gagal mendapat respons non-error, walau datanya belum tentu yang terbaru.
- **Partition Tolerance**: sistem tetap beroperasi walau komunikasi antar node terputus.

Karena partisi jaringan pasti bisa terjadi, perancang sistem harus memilih antara konsistensi dan ketersediaan:

Tabel 3. Pilihan AP dan CP

| Pilihan | Perilaku | Konsekuensi | Contoh di slide |
| --- | --- | --- | --- |
| AP | Permintaan selalu dilayani | Data bisa tidak sinkron | MongoDB |
| CP | Permintaan ditolak supaya data tetap konsisten | Layanan sementara tidak tersedia | Apache Cassandra |

Pengelompokan ini mengikuti materi kuliah. Pada praktiknya perilaku database tergantung konfigurasinya.

## 9. Konsistensi Data

- **Strong consistency**: hasil baca selalu sama di semua node. Cocok untuk bank.
- **Eventual consistency**: data akan konsisten setelah beberapa waktu. Cukup untuk media sosial.

## 10. Tantangan

- Latency (lama pemrosesan) dan throughput (jumlah yang diproses).
- Keamanan, misalnya DDoS dan Sybil attack. Sybil attack adalah pembuatan banyak identitas palsu untuk menguasai pengaruh, suara, atau reputasi di jaringan, terutama pada sistem P2P dan blockchain.
- Kompleksitas manajemen node.
- Tata kelola (governance) pada sistem terdesentralisasi.

## 11. Tren Masa Depan

1. Cloud-native (Kubernetes dan Docker)
2. Edge dan Fog Computing
3. Web3 dan DeFi
4. Federated learning (AI terdistribusi)

## 12. Pandangan Pribadi

Mengapa dunia semakin bergerak ke distribusi dan desentralisasi?

Menurut saya penyebab utamanya adalah ketergantungan pada satu titik. Ketika data dan pengguna terus bertambah, satu server tidak lagi cukup, dan bila server itu gagal maka seluruh layanan ikut berhenti. Distribusi menjawab masalah beban dan ketersediaan, sedangkan desentralisasi menjawab masalah kepercayaan karena kontrol tidak lagi dipegang satu pihak. Game online dengan banyak pemain, misalnya, jelas tidak bisa bergantung pada satu server saja.

Contoh sehari-hari yang paling terasa:

Menonton video di YouTube atau Netflix. Video diputar lancar dari server yang berada dekat dengan pengguna, dan layanan hampir tidak pernah benar-benar berhenti walau ada server yang bermasalah. Contoh lain adalah transfer lewat m-banking, yang membutuhkan konsistensi kuat agar saldo selalu benar.
