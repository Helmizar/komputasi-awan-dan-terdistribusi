# Jurnal Proses — Tugas 3

## Percobaan tanpa Lock
- Hasil `processed_count` yang didapat: 70 (dari total 100 pesanan).
- Kenapa bisa meleset (jelaskan mekanisme race condition dengan kata sendiri): Karena tidak ada pengaman (lock), beberapa thread pekerja mengambil dan membaca nilai processed_count secara bersamaan di sepersekian detik yang sama. Misalnya, pekerja A dan pekerja B sama-sama membaca angka 10. Keduanya lalu menambahkan angka 1 menjadi 11 dan menyimpannya kembali ke sistem. Akibatnya, ada dua pesanan yang diproses, namun hitungannya hanya bertambah satu karena pekerja B menimpa data pekerja A. Saling timpa data inilah yang disebut race condition sehingga hasil akhirnya selalu kurang dari jumlah seharusnya.

## Percobaan dengan Lock
- Hasil `processed_count` setelah perbaikan: 100 (selalu akurat dan tepat 100 pesanan). Penambahan with order_lock: membuat para thread pekerja tertib mengantre satu per satu saat akan mengubah variabel processed_count.

## Kendala Docker
- Error yang ditemui saat `docker build`/`docker run` dan cara memperbaikinya: 
- Eror 1 Saya menyadari bahwa aplikasi Docker Desktop belum menyala sempurna dan terminal berada di lokasi folder yang salah. Saya membuka aplikasi Docker Desktop dan mengarahkan terminal kembali ke direktori tugas dengan perintah cd.
- Eror 2 :failed to connect to the docker API / Docker Desktop is unable to start.
Cara memperbaiki: Error ini disebabkan oleh mesin (daemon) Docker yang gagal menyala karena kendala sistem Virtualisasi (WSL) di Windows. Saya memperbaikinya dengan mengecek status Virtualisasi di Task Manager, melakukan update Windows Subsystem for Linux (mengetikkan wsl --update di terminal as Administrator), lalu melakukan Restart/Reset pada Docker Desktop hingga indikatornya berubah menjadi Engine Running.

## Log Penggunaan AI (Level 2)

> Wajib diisi sesuai kebijakan Level 2 di [`../RUBRIK-UMUM.md`](../RUBRIK-UMUM.md). Tulis "Tidak memakai AI" pada baris pertama jika memang tidak dipakai. Hanya untuk brainstorming ide/outline — bukan untuk kode/analisis/teks akhir.

| Tanggal | Tool AI | Prompt yang diberikan | Ringkasan saran/ide AI | Bagaimana diolah jadi tulisan/kode sendiri |
|---|---|---|---|---|
| 03 Okt 2026 | Gemini | Bantu saya memahami kenapa thread yang berjalan bersamaan bisa membuat perhitungan meleset jika tanpa lock? Bisakah berikan perumpamaan sederhana? | AI memberikan perumpamaan tentang beberapa pekerja yang berebut satu pulpen dan satu buku tulis yang sama untuk mencatat pesanan, sehingga tulisan mereka saling menimpa (overwrite). | Saya menggunakan analogi "saling menimpa" tersebut untuk merangkai kalimat teknis tentang race condition di bagian "Kenapa bisa meleset" menggunakan pemahaman dan bahasa saya sendiri. |
| 03 Okt 2026 | Gemini | Saya mengalami error Docker Desktop is unable to start di Windows. Apa penyebab utama daemon docker gagal menyala? | AI mendiagnosis bahwa masalah utamanya sering kali terletak pada kegagalan Windows Subsystem for Linux (WSL) atau status Virtualisasi yang mati di level BIOS/Task Manager. AI menyarankan troubleshooting lewat wsl --update. | Saran dari AI saya praktikkan langsung ke sistem OS Windows saya. Pengalaman menyelesaikan hambatan konfigurasi hardware ini kemudian saya catat ke dalam blok "Kendala Docker" secara rinci. |
| ... | ... | ... | ... | ... |
| ... | ... | ... | ... | ... |
