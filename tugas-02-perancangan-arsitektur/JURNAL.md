# Jurnal Proses — Tugas 2

## [27 September 2026]
- Opsi arsitektur yang dipertimbangkan: ada awalnya kami mempertimbangkan untuk menggunakan SOA murni (seluruh service saling memanggil melalui API secara langsung) atau Publish-Subscribe murni (seluruh komunikasi dilempar ke dalam antrean/broker).
- Kenapa akhirnya pilih [SOA/Pub-Sub]:Kami menyadari SOA murni masih rawan bottleneck karena jika Service Pesanan memanggil Service Kurir secara langsung dan kurir lambat merespons, aplikasi akan delay. Sebaliknya, Pub-Sub murni tidak cocok untuk Service Pembayaran karena pelanggan butuh kepastian berhasil/gagal secara real-time (instan). Akhirnya, kami memilih kombinasi keduanya: SOA untuk Pembayaran (sinkron) agar pelanggan langsung mendapat kepastian, dan Pub-Sub / Message Broker untuk Katalog Resto & Kurir agar proses penyiapan dan pengantaran bisa berjalan paralel di latar belakang tanpa menahan layar aplikasi pelanggan.
- Revisi diagram (versi 1 → versi 2, apa yang berubah dan kenapa): -

## Log Penggunaan AI (Level 2)

> Wajib diisi sesuai kebijakan Level 2 di [`../RUBRIK-UMUM.md`](../RUBRIK-UMUM.md). Tulis "Tidak memakai AI" pada baris pertama jika memang tidak dipakai. Hanya untuk brainstorming ide/outline — bukan untuk kode/analisis/teks akhir.

| Tanggal | Tool AI | Prompt yang diberikan | Ringkasan saran/ide AI | Bagaimana diolah jadi tulisan/kode sendiri |
|---|---|---|---|---|
| 27/09/2026 | ChatGPT | Saya Memiliki case Tolong bantu menentukan apakah di case FoodGo berikut lebih sesuai menggunakan SOA atau Publish-Subscribe.  |AI menyarankan SOA sebagai arsitektur utama dan Publish-Subscribe sebagai pendukung komunikasi event secara asinkron.| Kelompok kami mempertimbangkan saran tersebut dan memilih kombinasi SOA + Publish-Subscribe sesuai kebutuhan FoodGo. |
| 27/09/2026 | ChatGPT | ... | ... | ... |
| 27/09/2026 | ChatGPT | ... | ... | ... |
