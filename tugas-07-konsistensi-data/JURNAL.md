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
| ... | ... | ... | ... | ... |
