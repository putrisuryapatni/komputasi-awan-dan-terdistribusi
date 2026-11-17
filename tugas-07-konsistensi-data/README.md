# Tugas 7 (Pekan 7) — Konsistensi Data

**Materi terkait:** Data-Centric Consistency (Sequential, Eventual), Client-Centric Consistency.

## Studi Kasus

Data saldo *e-wallet* pelanggan FoodGo berbeda antara server wilayah **Jakarta** dan **Bandung** karena replikasi antar server berjalan lambat. Contoh kasus nyata: pelanggan top-up saldo lewat server Jakarta, lalu dalam hitungan detik memesan makanan lewat aplikasi yang tersambung ke server Bandung — saldo belum ter-update, transaksi ditolak padahal saldo sebenarnya cukup.

## Tugas Kelompok

### Bagian 1 — Analisis (wajib)

1. Tentukan: apakah kasus ini **membutuhkan Sequential Consistency** (semua replika melihat urutan operasi yang sama) atau **cukup Eventual Consistency** (replika akan konsisten "pada akhirnya", boleh sementara berbeda)? Berikan argumen berbasis dampak bisnis (mis. kasus saldo *e-wallet* jauh lebih sensitif dibanding kasus "jumlah like" di media sosial).
Untuk kasus saldo e-wallet pada aplikasi FoodGo, Eventual Consistency murni TIDAK CUKUP dan dapat merusak pengalaman pengguna serta integritas sistem. Kasus ini membutuhkan jaminan konsistensi yang lebih kuat seperti Sequential Consistency secara global, atau yang lebih efisien dari sisi latensi, yaitu Client-Centric Consistency.
Argumen Bisnis & Dampak Finansial:
- Sensitivitas Data Keuangan: Saldo e-wallet merepresentasikan uang nyata. Keterlambatan sinkronisasi (replication lag) berakibat fatal secara finansial dan hukum.
- Pengalaman Pengguna (UX) yang Buruk: Pelanggan yang baru saja melakukan top-up dengan sadar memiliki saldo, namun mendapati transaksinya ditolak (false rejection) akan merasa frustrasi, kehilangan kepercayaan, dan berpotensi meninggalkan aplikasi.
- Risiko Eksploitasi (Double Spending / Race Condition): Jika replikasi lambat, pengguna dapat mencoba memesan di beberapa restoran sekaligus menggunakan saldo yang sama sebelum server lain menyadari saldo tersebut sudah terpotong.
- Kontras dengan Media Social (Mis. Like): Keterlambatan melihat jumlah like bertambah 5–10 detik di media sosial tidak menimbulkan kerugian finansial atau kegagalan aksi kritis, sehingga eventual consistency sudah memadai untuk kasus tersebut.

2. Jika kalian menyimpulkan Eventual Consistency saja **tidak cukup** untuk skenario ini, usulkan pendekatan **Client-Centric Consistency** yang relevan (mis. *Read-Your-Writes*: pelanggan yang baru top-up harus selalu melihat saldo terbarunya sendiri, walau replika lain belum sinkron).
Karena penegakan Sequential Consistency secara ketat di seluruh dunia/wilayah dapat menambah latensi tinggi pada setiap operasi tulis, pendekatan Client-Centric Consistency, khususnya Read-Your-Writes Consistency adalah solusi yang paling relevan dan efisien.
- Definisi Mekanisme:
Read-Your-Writes menjamin bahwa klien (pelanggan) yang melakukan suatu write (misal: top-up saldo ke server Jakarta) akan selalu melihat hasil write tersebut pada operasi read berikutnya, meskipun read tersebut dilayani oleh server replika yang berbeda (misal: server Bandung).
- Cara Kerja Teknis:
Setiap kali klien melakukan write (top-up), server yang menerima write memberikan version number atau timestamp (misal: version = 5).
Saat klien melakukan read atau transaksi berikutnya di server lain (Bandung), klien menyertakan informasi versi terakhir yang ia miliki.
- Server Bandung akan mengecek: "Apakah data saya sudah mencapai versi 5?" Jika belum, server Bandung akan melakukan sync/pull dari server Jakarta terlebih dahulu atau menunda transaksi hingga data sinkron sebelum mengeksekusi pesanan pelanggan tersebut.

3. Gambarkan **diagram alir urutan (sequence diagram)** yang menunjukkan operasi read/write antara pelanggan, server Jakarta, server Bandung, dan proses replikasi — untuk skenario **tanpa** jaminan konsistensi (bug terjadi) DAN skenario **dengan** solusi yang kalian usulkan (bug teratasi). Pakai Mermaid (`sequenceDiagram`) di `README.md` — lihat contoh cara pakai di Tugas 2.

### Diagram alir urutan (sequence diagram)
1. Tanpa jaminan konsistensi (bug terjadi):
```mermaid
sequenceDiagram
    autonumber
    actor P as Pelanggan
    participant A as Aplikasi FoodGo
    participant J as Server Jakarta
    participant R as Proses Replikasi (Timer 2 detik)
    participant B as Server Bandung

    Note over J,B: Kondisi awal - Jakarta: saldo 0 versi 0 - Bandung: saldo 0 versi 0

    Note over P,B: FASE 1 - TOP-UP DI JAKARTA (t = 0 detik)
    P->>A: Top-up Rp50.000
    A->>J: top_up(user1, 50000)
    J->>J: Tulis saldo = 50000, versi = 1
    J-->>A: Top-up berhasil
    A-->>P: Notifikasi top-up sukses
    J->>R: Jadwalkan replikasi ke Bandung (tertunda 2 detik)
    Note over R: Timer berjalan, data belum dikirim

    Note over P,B: FASE 2 - PESAN MAKANAN DI BANDUNG (t = 0.5 detik)
    P->>A: Pesan makanan
    A->>B: baca_saldo_naif(user1)
    B->>B: Cek data lokal, hasilnya belum ada (versi 0)
    B-->>A: saldo = 0 (STALE / data basi)
    alt Saldo 0 lebih kecil dari harga pesanan
        A-->>P: Transaksi DITOLAK
    end
    Note over P,B: BUG - saldo sebenarnya 50000 tetapi pelanggan ditolak

    Note over P,B: FASE 3 - REPLIKASI TIBA (t = 2 detik)
    R->>B: write saldo = 50000, versi = 1
    B->>B: Update saldo = 50000, versi = 1
    Note over J,B: Jakarta dan Bandung kini konsisten, tetapi sudah terlambat
```

2. Dengan solusi yang usulkan (bug teratasi):
```mermaid
sequenceDiagram
    autonumber
    actor P as Pelanggan
    participant A as Aplikasi FoodGo
    participant J as Server Jakarta
    participant R as Proses Replikasi (Timer 2 detik)
    participant B as Server Bandung

    Note over J,B: Kondisi awal - Jakarta: saldo 50000 versi 1 - Bandung: saldo 50000 versi 1

    Note over P,B: FASE 1 - TOP-UP DI JAKARTA (t = 0 detik)
    P->>A: Top-up Rp20.000
    A->>J: top_up(user1, 20000)
    J->>J: Tulis saldo = 70000, versi = 2
    J-->>A: Top-up berhasil, versi terakhir = 2
    A->>A: Simpan versi_minimal = 2
    A-->>P: Notifikasi top-up sukses
    J->>R: Jadwalkan replikasi ke Bandung (tertunda 2 detik)
    Note over B: Bandung masih saldo 50000 versi 1 (belum terbaru)

    Note over P,B: FASE 2 - BACA SALDO DENGAN SYARAT VERSI (t = 0.5 detik)
    P->>A: Pesan makanan
    A->>B: baca_saldo_read_your_writes(user1, versi_minimal = 2)
    B->>B: Bandingkan versi lokal (1) dengan versi_minimal (2)
    Note over B: Versi lokal lebih kecil, Bandung TIDAK boleh menjawab dulu
    loop Polling setiap 0.2 detik sampai versi lokal >= 2
        B->>B: Cek ulang versi lokal, menunggu sinkronisasi
    end

    Note over P,B: FASE 3 - REPLIKASI TIBA DAN PEMBACAAN BERHASIL (t = 2 detik)
    R->>B: write saldo = 70000, versi = 2
    B->>B: Update saldo = 70000, versi = 2
    B->>B: Cek ulang, versi lokal 2 memenuhi versi_minimal 2
    B-->>A: saldo = 70000 (BENAR)
    alt Saldo 70000 cukup untuk harga pesanan
        A-->>P: Transaksi DIPROSES
    end
    Note over P,B: Bug teratasi - pelanggan selalu membaca hasil tulisannya sendiri
```


### Bagian 2 — Simulasi (opsional, nilai tambah)

Skeleton `src/replica_simulation.py` mensimulasikan dua replika (Jakarta & Bandung) dengan delay replikasi buatan. Lengkapi TODO untuk:
1. Menunjukkan kondisi **inconsistent read** (replika Bandung masih menunjukkan saldo lama walau Jakarta sudah update).
2. Mengimplementasikan mekanisme **Read-Your-Writes** sederhana (mis. dengan version/timestamp per client) yang memperbaiki masalah tersebut.

```bash
python3 src/replica_simulation.py
```

## Struktur Submission

```
tugas-07-konsistensi-data/
├── README.md      # Analisis konsistensi + sequence diagram Mermaid
├── JURNAL.md
├── src/            # (opsional) simulasi replikasi
└── bukti/          # (jika mengerjakan simulasi) output program
```

## Rubrik Penilaian (Tugas 7)

| Komponen | Bobot | Kriteria |
|---|---|---|
| Ketepatan pemilihan model konsistensi | 30% | Argumen berbasis dampak bisnis nyata, bukan hafalan definisi |
| Kualitas sequence diagram | 30% | Menunjukkan jelas titik kegagalan & titik perbaikan |
| Usulan solusi client-centric | 25% | Solusi konkret dan dapat diimplementasikan (bukan wacana abstrak) |
| Proses & kontribusi kelompok | 15% | `JURNAL.md`, commit history (+ bonus jika simulasi dikerjakan) |

## Batasan Penggunaan AI (Level 2)

Kebijakan **Level 2 (AI Assisted Idea Generation & Structuring)** berlaku — lihat [`../RUBRIK-UMUM.md`](../RUBRIK-UMUM.md). Boleh bertanya konsep umum consistency model ke AI untuk brainstorming; **tidak boleh** meminta AI menuliskan analisis akhir/sequence diagram atau isi `# TODO` di `src/replica_simulation.py` yang tinggal ditempel. Catat pemakaian AI di "Log Penggunaan AI" pada `JURNAL.md`.

- Sebagai bukti pemahaman konsep (bukan hafalan jawaban skenario FoodGo saja), `README.md` wajib menyertakan **satu skenario tambahan** yang kalian buat sendiri (mis. kasus lain di aplikasi ojek online) dan menentukan model konsistensi yang tepat untuk skenario tambahan tersebut.

- Kasus Nyata: Sebuah platform e-commerce mengadakan flash sale ponsel pintar populer dengan stok yang sangat terbatas (misalnya sisa 5 unit saja) di server wilayah yang tersebar (Jakarta, Surabaya, Medan). Ribuan pengguna dari berbagai kota menyerbu secara serentak. Jika sistem hanya mengandalkan Eventual Consistency murni, server di Surabaya mungkin belum tahu bahwa stok sudah habis dibeli oleh pengguna di Jakarta beberapa milidetik sebelumnya. Akibatnya, sistem menerima pesanan dari dua wilayah untuk unit yang sama, menyebabkan overselling (barang terjual melebihi stok fisik yang ada) dan berujung pada kekacauan inventaris serta kekecewaan massal.

- Model Konsistensi yang Tepat: Linearizability (Strong Consistency) dengan bantuan sistem penguncian terdistribusi (distributed locking) atau basis data terpusat yang konsisten secara instan (atomic operations).

- Alasan Pemilihan:
Inventaris barang dengan jumlah terbatas adalah data yang sangat kritis dan bersifat global. Tidak boleh ada dua pengguna di server manapun yang berhasil mengklaim unit terakhir di waktu yang hampir bersamaan. Setiap operasi pengurangan stok harus bersifat atomik dan langsung terlihat secara seragam di seluruh titik server detik itu juga untuk mencegah penjualan ganda.
