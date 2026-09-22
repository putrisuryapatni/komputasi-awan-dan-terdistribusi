# Jurnal Proses — Tugas 1


## [21-09-2026]
- Peserta: Ida Ayu Putri S. B. dan I Wayan Juanesa R. P.
- Poin diskusi: 
    - Nentuin pitfall yang mana aja dulu
    - Tugek nemu pitfall the network is reliable dan latency is zero
    - Di bagian yang "# network is always reliable, no need for retry" langsung ketahuan kalau ada yang salah, karena pada soal dikatakan adanya kendala pada aplikasi tapi ini malah dibilang selalu berjalan dengan balik
    - Pada bagian "tidak ada timeout sama sekali pada pemanggilan antar service" juga langsung ketahuan bahwa ada yang salah di latency nya
    - Ryan menemukan 2 pitfal lain yaitu "Single Point of Failure" dan "Always-On Assumption"
    - pada soal terlihat "satu server yang menangani semua modul(...)kewalahan" disini juga sudah dibilang satu server menangani semua modul sehingga menemukan pitfal Single Point of Failure
    - untuk bagian "Server backend kadang *crash* total dan perlu di-restart manual." setelah saya brainstroming ke AI mengenai kemungkinan pitfal yang cocok dan saya menemukan pitfal Always-On Assumption
- Perbedaan pendapat (jika ada): ...

## [22-09-2026]
- Peserta: Ida Ayu Putri S. B. dan I Wayan Juanesa R. P.
- Poin diskusi: 
    - Belajar cara githubnya karena masih bingung dan ada sedikit error
    - Pastiin lagi semua analisisnya udah bener, cek lagi solusi yang kita kasi udah sesuai sama ketentuan yang diminta

## Review Silang
- [Ryan] mengomentari analisis [Tugek]: Dari yang sudah saya liat hasil dari analisis punya tugek, sudah sesuai dengan hasil diskusi kita di awal tidak adanya yang melenceng secara tiba-tiba dan di buat dengan sebaik mungkin
- [Tugek] mengomentari analisis [Ryan]: Kalau saya lihat, semua permasalahan yang ditemukan ryan sudah sesuai dan juga solusinya sudah seperti ketentuan yang ada pada soal

## Log Penggunaan AI (Level 2)

> Wajib diisi sesuai kebijakan Level 2 di [`../RUBRIK-UMUM.md`](../RUBRIK-UMUM.md). Tulis "Tidak memakai AI" pada baris pertama jika memang tidak dipakai. Hanya untuk brainstorming ide/outline — bukan untuk kode/analisis/teks akhir.

| Tanggal | Tool AI | Prompt yang Diberikan | Ringkasan Saran / Ide AI | Bagaimana Diolah Jadi Tulisan / Kode Sendiri |
| :--- | :--- | :--- | :--- | :--- |
| 21-09-2026 | GPT | Apa itu pitfall | Pitfall adalah sesuatu yang awalnya dianggap aman atau benar, tetapi ternyata bisa menyebabkan masalah ketika kondisi tertentu terjadi. | Tidak ada, karena sebelum membuat tugas ini saya belum tahu apa itu pitfall. |
| 21-09-2026 | GPT | Bantu saya melakukan brainstorming untuk mengidentifikasi potensi pitfall dari: "Server backend kadang *crash* total dan perlu di-restart manual. Kenapa begitu?" | Karena sistem menganggap server akan selalu aktif, padahal server bisa *crash*. Saat *crash* terjadi, sistem tidak punya mekanisme pemulihan otomatis sehingga harus di-restart manual. | Sistem tidak boleh menganggap bahwa server tidak akan pernah mengalami kegagalan, serta tidak boleh bergantung pada manusia untuk melakukan *restart* manual. |
| 21-09-2026 | GPT | Jika satu server yang menangani semua modul (pesanan, pembayaran, notifikasi kurir) kewalahan karena semuanya berjalan di satu proses monolitik yang sama, apa solusi dan *trade-off* yang cocok? | **Solusi:** Memisahkan modul paling berat menjadi beberapa *service* dan menggunakan *queue* untuk proses non-instan.<br><br>**Trade-off:** Menambah kompleksitas pengelolaan dan komunikasi antarservice, namun beban server terbagi dan tahan lonjakan trafik. | **Solusi:** Perlu server cadangan untuk membantu server utama melayani permintaan, serta menggunakan sistem antrean (*queue*) agar diproses bertahap sesuai kapasitas.<br><br>**Trade-off:** Membutuhkan waktu tunggu lebih lama pada antrean dan meningkatkan biaya karena ada pengelolaan server tambahan. |
| 20-09-2026 | Gemini | jelaskan maksud dari semua pitfall ini, karena saya masih asing dan belum terlalu paham
"the network is reliable", "latency is zero", "bandwidth is infinite", "the network is secure", "topology doesn't change", "there is one administrator", "transport cost is zero", "the network is homogeneous" | 1. The network is reliable (Jaringan itu selalu andal/pasti terhubung) 2. Latency is zero (Delay/waktu tunggu jaringan itu nol) 3. Bandwidth is infinite (Kapasitas/lebar pita jaringan itu tanpa batas) 4. The network is secure (Jaringan itu sudah pasti aman) 5. Topology doesn't change (Struktur/peta jaringan tidak pernah berubah) 6. There is one administrator (Hanya ada satu administrator yang mengatur seluruh sistem) 7. Transport cost is zero (Biaya pengiriman data itu gratis) 8. The network is homogeneous (Semua perangkat di jaringan menggunakan standar/teknologi yang sama) | Ini saya jadikan landasan awal untuk analisis masalah yang ada dalam kasus, ini adalah patokan saya untuk menemukan pitfall 1 dan 2 |
| 22-09-2026 | Gemini | saya ingin adanya solusi pemisahan layanan, dengan tujuan agar fokus aplikasi dapat terpecah sesuai dengan layanan masing masing dan menghindari crash, dari solusi yang saya inginkan kira kira arsitektur apa yang paling cocok? | Berdasarkan kebutuhan utama Anda—yaitu memecah fokus aplikasi berdasarkan layanannya masing-masing (separation of concerns) dan mencegah terjadinya crash total (fault isolation)—arsitektur yang paling cocok adalah Microservices Architecture berbasis Event-Driven (Pub-Sub), atau yang di ranah akademis sering dikombinasikan sebagai SOA (Service-Oriented Architecture) berbasis Microservices & Pub-Sub. | Kami mengolah ide dari Gemini ini menjadi kesimpulan kelompok yang padu. |
