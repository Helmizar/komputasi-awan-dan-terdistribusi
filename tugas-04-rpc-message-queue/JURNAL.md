# Jurnal Proses — Tugas 4

## Jalur yang dipilih
- Keduanya (RPC dan MQ), alasan: Kami memilih kedua jalur untuk memahami perbedaan komunikasi sinkron dan asinkron pada sistem FoodGo. RPC digunakan ketika suatu modul membutuhkan respons secara langsung dari modul lain, sedangkan Message Queue (MQ) menggunakan RabbitMQ untuk mengirim pesan secara asinkron tanpa mengharuskan pengirim menunggu pesan selesai diproses oleh penerima. Dengan mengerjakan kedua jalur, saya dapat memahami penerapan masing-masing pola komunikasi sesuai kebutuhan sistem.

## Kendala teknis
- 

## Uji "pesan tidak hilang" (khusus Jalur B)
- Langkah uji:
  - Mematikan consumer agar tidak menerima pesan.
  - Menjalankan publisher untuk mengirim pesan ke antrean pembayaran_berhasil.
  - Memeriksa RabbitMQ Management untuk melihat apakah pesan tersimpan dalam antrean.
  - Menjalankan consumer kembali.
  - Memeriksa apakah pesan yang tertunda berhasil diterima dan diproses.
- Hasil yang diamati: Publisher berhasil dijalankan dan menghasilkan tiga event pembayaran untuk user1 sebesar Rp20.000, user2 sebesar Rp40.000, dan user3 sebesar Rp60.000. Setelah consumer dijalankan kembali, ketiga event tersebut berhasil diterima dan ditampilkan sebagai notifikasi pembayaran oleh modul Kurir/Notifikasi.
Hasil pengujian menunjukkan bahwa pesan yang dikirim oleh publisher dapat diterima oleh consumer setelah consumer dijalankan kembali. Hal ini menunjukkan bahwa RabbitMQ memungkinkan pengiriman pesan secara asinkron sehingga publisher tidak harus menunggu consumer aktif untuk mengirimkan event pembayaran. 

## Log Penggunaan AI (Level 2)

> Wajib diisi sesuai kebijakan Level 2 di [`../RUBRIK-UMUM.md`](../RUBRIK-UMUM.md). Tulis "Tidak memakai AI" pada baris pertama jika memang tidak dipakai. Hanya untuk brainstorming ide/outline — bukan untuk kode/analisis/teks akhir.

| Tanggal | Tool AI | Prompt yang diberikan | Ringkasan saran/ide AI | Bagaimana diolah jadi tulisan/kode sendiri |
|---|---|---|---|---|
| 10 Okt 2026 | Gemini | Apa fungsi sebenarnya dari perintah ch.basic_ack pada consumer RabbitMQ? Apa bahayanya jika seandainya baris kode tersebut saya hapus? | ack (acknowledgement) adalah semacam "tanda terima". Jika dihapus, RabbitMQ akan mengira pesannya belum dikerjakan dan akan terus menahannya di antrean atau mengirimkannya ulang, yang bisa memicu pesanan ganda (double order). | Saya menggunakan ide konseptual ini untuk benar-benar memahami mengapa sistem asinkron sangat aman dari kegagalan. Pemahaman ini memperkuat argumen analisis saya di README.md tentang stabilitas antrean. |
| 10 Okt 2026 | Gemini | Di dashboard RabbitMQ, apa arti dari grafik 'Queued messages' yang tiba-tiba naik ketika saya mematikan modul consumer? | Grafik tersebut adalah bukti visual dari konsep Asynchronous Decoupling, di mana broker secara aktif menahan dan melindungi pesan di dalam memori/disknya (buffering) karena mendeteksi penerimanya sedang offline. | Penjelasan dari AI ini sangat membantu saya dalam menginterpretasikan screenshot grafik RabbitMQ. Saya mengolah penjelasan ini dengan bahasa sendiri sebagai narasi pembuktian skenario di file README.md. |
| 10 Okt 2026 | Claude | Bantu saya membuat analisis perbandingan kapan harus menggunakan RPC dan kapan harus menggunakan Message Queue berdasarkan skenario FoodGo. Selain itu, sebutkan contoh fatal apa yang akan terjadi jika pengunaannya tertukar (misal RPC dipakai untuk kurir atau MQ dipakai untuk pembayaran). | AI menjelaskan bahwa RPC sangat tepat untuk operasi yang menuntut kepastian instan (immediate consistency), sedangkan Message Queue ideal untuk proses latar belakang yang butuh keamanan data meski server penerima mati (fault tolerance). AI juga memaparkan ide risiko kerugian finansial (jika MQ dipakai untuk pembayaran) dan risiko pengguna gagal bayar (jika RPC dipakai untuk kurir) | Saya menyaring ide-ide tersebut dan mengaitkannya secara langsung dengan bukti eksperimen kelompok kami. Bukti simulasi blocking pada klien RPC saya jadikan dasar argumen untuk validasi pembayaran. Sementara itu, bukti eksperimen matinya consumer (KeyboardInterrupt) pada RabbitMQ saya jadikan fondasi argumen untuk modul kurir. Saya kemudian merangkai ide kausalitas (sebab-akibat) dari AI menjadi dua paragraf analisis utuh dengan bahasa saya sendiri |
|10 Oktober 2026 | ChatGPT | Jelaskan konsep RPC (Remote Procedure Call) dan cara kerjanya | RPC memungkinkan client memanggil fungsi yang tersedia di server melalui komunikasi jaringan. | Saya menggunakan penjelasan tersebut untuk memahami konsep RPC dan menuliskannya kembali dengan bahasa sendiri. |
| 10 Oktober 2026 | ChatGPT | Jelaskan arti waktu eksekusi pada client RPC dan mengapa pemanggilan fungsi membutuhkan waktu. | Waktu eksekusi dapat mencakup pengiriman permintaan, pemrosesan server, dan penerimaan respons oleh client.| Saya menggunakan penjelasan tersebut untuk memahami waktu eksekusi yang muncul pada hasil pengujian program.|
