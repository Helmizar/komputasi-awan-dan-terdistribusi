# Jurnal Proses — Tugas 1

> Isi jurnal ini selama proses diskusi berlangsung, bukan ditulis ulang rapi di akhir. Tulis dengan gaya bebas — poin diskusi, kebuntuan, perubahan pikiran.

## [Tanggal diskusi 1]
- Peserta: [nama-nama yang hadir]
- Poin diskusi: ...
- Perbedaan pendapat (jika ada): ...

## [Tanggal diskusi 2]
- ...

## Review Silang
- [Nama] mengomentari analisis [Nama lain]: ...

## Log Penggunaan AI (Level 2)

> Wajib diisi sesuai kebijakan Level 2 di [`../RUBRIK-UMUM.md`](../RUBRIK-UMUM.md). Tulis "Tidak memakai AI" pada baris pertama jika memang tidak dipakai. Hanya untuk brainstorming ide/outline — bukan untuk kode/analisis/teks akhir.

| Tanggal | Tool AI | Prompt yang diberikan | Ringkasan saran/ide AI | Bagaimana diolah jadi tulisan/kode sendiri |
|---|---|---|---|---|
| 19/09/2026 | Gemini Ai | Saya ingin mengangkat pitfall 'The network is reliable' untuk kasus FoodGo. Bisakah kamu membantu mem-brainstorming area mana saja dalam sistem terdistribusi yang biasanya terdampak asumsi ini, agar saya bisa lebih mudah mencari buktinya di teks skenario? | Gemini memberikan kerangka berpikir berupa 6 area utama dalam sistem terdistribusi yang biasanya bermasalah akibat asumsi bahwa jaringan selalu andal. Gemini secara khusus menyoroti konsep-konsep teknis seperti terjadinya Thread atau Connection Exhaustion (kehabisan memori karena thread menumpuk) serta Cascading Failures (kegagalan berantai antar servis). Selain itu, AI juga membantu merangkumkan checklist bukti spesifik dari skenario FoodGo, seperti adanya komentar # network is always reliable, ketiadaan timeout pada komunikasi antar-modul, dan arsitektur monolitik. | Dari berbagai ide yang diberikan , saya menyaring dan hanya mengambil poin-poin yang benar-benar relevan dengan skenario FoodGo, yaitu masalah komunikasi inter-service dan ketiadaan manajemen batas waktu. Saya kemudian menggunakan konsep teknis bawaan AI (seperti thread exhaustion) murni sebagai dasar logika pemikiran saya. Setelah itu, saya merangkai sendiri analisis dampaknya menggunakan bahasa saya pribadi untuk menjelaskan urutan kejadian—mulai dari ketiadaan timeout, menumpuknya request, hingga akhirnya server crash. Terakhir, saya mengutip langsung bukti dari teks skenario dan memadukannya dengan gagasan solusi Timeout yang tepat ke dalam file README.md. |
