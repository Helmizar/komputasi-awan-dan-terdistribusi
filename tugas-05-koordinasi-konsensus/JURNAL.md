# Jurnal Proses — Tugas 5

## Implementasi Bully
- Kendala saat mengisi TODO 1-4: Tantangan utama ada pada penanganan waktu tunggu (timeout) saat node menunggu balasan pesan OK. Jika ditunggu secara sekuensial tanpa pengaman, proses bisa tertahan (blocking). Solusinya adalah menggunakan objek threading.Event() dengan batas waktu wait(timeout=ELECTION_TIMEOUT). Selain itu, saat sebuah node menerima pesan ELECTION dan membalas OK, proses pemilihan diri sendiri (start_election) harus dijalankan di thread terpisah (asynchronous thread) agar tidak mengunci socket handler utama.

## Skenario 1: Start normal
- Leader yang terpilih: ...
- Log/observasi: ...

## Skenario 2: Leader dimatikan
- Leader baru yang terpilih: ...
- Berapa lama waktu sampai leader baru terdeteksi: ...

## Log Penggunaan AI (Level 2)

> Wajib diisi sesuai kebijakan Level 2 di [`../RUBRIK-UMUM.md`](../RUBRIK-UMUM.md). Tulis "Tidak memakai AI" pada baris pertama jika memang tidak dipakai. Hanya untuk brainstorming ide/outline — bukan untuk kode/analisis/teks akhir.

| Tanggal | Tool AI | Prompt yang diberikan | Ringkasan saran/ide AI | Bagaimana diolah jadi tulisan/kode sendiri |
|---|---|---|---|---|
| 15 Okt 2026 | Gemini | Dalam algoritma Bully, bagaimana cara terbaik menangani waktu tunggu balasan pesan OK di Python agar thread tidak terhenti selamanya? |  AI menyarankan penggunaan objek `threading.Event()` dengan method `.wait(timeout=...)` daripada melakukan looping manual dengan `time.sleep()`. | Saya mengimplementasikan `self.ok_received = threading.Event()` pada konstruktor `__init__`, lalu menggunakan `.wait(timeout=ELECTION_TIMEOUT)` di dalam fungsi `start_election` agar node tahu kapan harus menyatakan diri sebagai leader jika tidak ada balasan. |
| 15 Okt 2026 | Gemini | mengapa sistem terdistribusi seperti FoodGo membutuhkan Node Leader tunggal untuk urutan pesanan daripada membiarkan semua node mencatat waktu lokal masing-masing? | AI menjelaskan masalah physical clock skew/drift (perbedaan milidetik antar jam fisik server) yang membuat urutan pesanan kacau, sehingga diperlukan koordinator pusat (*centralized sequencer*) untuk menentukan urutan logis yang absolut.  | Penjelasan konseptual dari AI tersebut saya gunakan sebagai bahan argumen utama dalam menyusun analisis 'Kenapa Perlu Leader' di file `README.md` menggunakan kalimat saya sendiri. |
| ... | ... | ... | ... | ... |
