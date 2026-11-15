# Jurnal Proses — Tugas 7

## Diskusi pemilihan model konsistensi
- Argumen yang dipertimbangkan: ...
- Kesimpulan akhir & alasan: ...

## Simulasi (jika dikerjakan)
- Hasil skenario naif vs Read-Your-Writes: ...

## Log Penggunaan AI (Level 2)

> Wajib diisi sesuai kebijakan Level 2 di [`../RUBRIK-UMUM.md`](../RUBRIK-UMUM.md). Tulis "Tidak memakai AI" pada baris pertama jika memang tidak dipakai. Hanya untuk brainstorming ide/outline — bukan untuk kode/analisis/teks akhir.

| Tanggal | Tool AI | Prompt yang diberikan | Ringkasan saran/ide AI | Bagaimana diolah jadi tulisan/kode sendiri |
|---|---|---|---|---|
| 15 Nov 2026 | ChatGPT | Apa perbedaan mendasar antara Sequential Consistency dan Eventual Consistency dari sisi dampaknya ke pengguna akhir? Saya sedang brainstorming untuk menentukan mana yang lebih cocok untuk sistem yang melibatkan data finansial | AI menjelaskan bahwa perbedaan utamanya ada di toleransi terhadap inkonsistensi sementara, Eventual Consistency mentoleransi jeda, Sequential Consistency tidak. AI juga menyebut contoh kasus seperti saldo rekening bank sebagai contoh data yang tidak boleh inkonsisten meski sebentar. | Konsep dari AI saya jadikan landasan untuk membandingkan skenario FoodGo dengan kasus lain yang tidak sensitif (rating driver ojek online). Argumen berbasis dampak bisnis saya susun sendiri berdasarkan pemahaman tersebut. |
| 15 Nov 2026 | ChatGPT | Saya sedang mencoba memahami konsep Read-Your-Writes sebagai salah satu bentuk Client-Centric Consistency. Bagaimana cara kerja umumnya di level teknis, misalnya mekanisme apa yang biasa dipakai sistem terdistribusi untuk mengimplementasikannya? | AI menjelaskan mekanisme umum seperti penggunaan version token atau timestamp yang disisipkan ke sesi pengguna, serta konsep bagaimana server penerima bisa mem-forward permintaan ke server yang lebih up-to-date. | Mekanisme version token dari AI saya adaptasi ke konteks FoodGo (server Jakarta dan Bandung). Alur teknis dan narasi penjelasannya saya tulis ulang sendiri menggunakan contoh top-up saldo yang konkret. |
| 15 Nov 2026 | Gemini | "Pada sistem saldo e-wallet dengan replikasi multi-region (Jakarta-Bandung) yang mengalami delay jaringan, mengapa model Eventual Consistency tidak cukup dan bisa merugikan bisnis?" | AI menjelaskan bahwa saldo uang adalah data finansial yang sangat sensitif. Jika hanya mengandalkan Eventual Consistency murni, ada celah waktu (inconsistency window) di mana user yang baru top-up membaca saldo lama Rp0 di replika lain sehingga transaksi ditolak (false rejection), memicu komplain pelanggan dan hilangnya pendapatan. | Saya menggunakan pemaparan dampak bisnis nyata tersebut sebagai landasan argumen pada analisis README.md untuk menegaskan mengapa FoodGo memerlukan jaminan konsistensi yang lebih kuat (Read-Your-Writes). |
| 15 Nov 2026 | Gemini | "Bagaimana logika kerja mekanisme Client-Centric Consistency (Read-Your-Writes) dalam menangani replika yang datanya masih tertinggal?" | AI menyarankan pendekatan version tracking. Client menyimpan nomor versi dari transaksi tulis terakhirnya. Saat membaca dari suatu replika, sistem mengecek nomor versi data tersebut; jika versinya lebih kecil dari milik client, pembacaan sementara dialihkan (fallback/redirect) ke server utama yang sudah mutakhir. | Ide pengecekan versi dan redirect ini saya terjemahkan menjadi baris kode fungsi baca_saldo_read_your_writes di src/replica_simulation.py, serta saya gunakan untuk menyusun alur Sequence Diagram perbaikan di README.md. |
| ... | ... | ... | ... | ... |
