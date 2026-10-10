# Jurnal Proses — Tugas 4

## Jalur yang dipilih
- [RPC / MQ / keduanya], alasan: ...

## Kendala teknis
- Error saat setup (mis. koneksi RabbitMQ ditolak, port bentrok): ...

## Uji "pesan tidak hilang" (khusus Jalur B)
- Langkah uji: matikan consumer → jalankan publisher → nyalakan consumer
- Hasil yang diamati: ...

## Log Penggunaan AI (Level 2)

> Wajib diisi sesuai kebijakan Level 2 di [`../RUBRIK-UMUM.md`](../RUBRIK-UMUM.md). Tulis "Tidak memakai AI" pada baris pertama jika memang tidak dipakai. Hanya untuk brainstorming ide/outline — bukan untuk kode/analisis/teks akhir.

| Tanggal | Tool AI | Prompt yang diberikan | Ringkasan saran/ide AI | Bagaimana diolah jadi tulisan/kode sendiri |
|---|---|---|---|---|
| 10 Okt 2026 | Gemini | Apa fungsi sebenarnya dari perintah ch.basic_ack pada consumer RabbitMQ? Apa bahayanya jika seandainya baris kode tersebut saya hapus? | ack (acknowledgement) adalah semacam "tanda terima". Jika dihapus, RabbitMQ akan mengira pesannya belum dikerjakan dan akan terus menahannya di antrean atau mengirimkannya ulang, yang bisa memicu pesanan ganda (double order). | Saya menggunakan ide konseptual ini untuk benar-benar memahami mengapa sistem asinkron sangat aman dari kegagalan. Pemahaman ini memperkuat argumen analisis saya di README.md tentang stabilitas antrean. |
| 10 Okt 2026 | Gemini | Di dashboard RabbitMQ, apa arti dari grafik 'Queued messages' yang tiba-tiba naik ketika saya mematikan modul consumer? | Grafik tersebut adalah bukti visual dari konsep Asynchronous Decoupling, di mana broker secara aktif menahan dan melindungi pesan di dalam memori/disknya (buffering) karena mendeteksi penerimanya sedang offline. | Penjelasan dari AI ini sangat membantu saya dalam menginterpretasikan screenshot grafik RabbitMQ. Saya mengolah penjelasan ini dengan bahasa sendiri sebagai narasi pembuktian skenario di file README.md. |
| ... | ... | ... | ... | ... |
| ... | ... | ... | ... | ... |
