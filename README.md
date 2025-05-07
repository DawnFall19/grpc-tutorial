# Refleksi Modul 8
1. - Unary melibatkan satu `request` dan satu `response`, sehingga cocok untuk transaksi sederhana yang sekali pakai. Contoh skenarionya adalah permintaan identitas pengguna, dimana respon yang diberikan akan berisi seluruh data diri pengguna sekaligus.
   - Server Streaming melibatkan satu `request` dan beberapa `response` sehingga cocok untuk data feed atau pagination. Contoh skenarionya adalah permintaan riwayat transaksi pengguna dalam pagination 10, dimana respon akan mengirimkan riwayat transaksi 10 demi 10.
   - Bidirectional Streaming melibatkan beberapa `request` dan beberapa `response` sehingga cocok untuk transaksi data yang terus menerus. Contoh skenarionya adalah sistem chatting dimana request terus menerus dibuat dan diteruskan ke server sebelum dilanjutkan sebagai response pada pengguna yang lain tanpa memutus koneksi.

2. - Menerapkan TLS pada semua lalu lintas gRPC
   - Menggunakan autentikasi misalnya JWT yang divalidasi secara berkala untuk memastikan masa berlaku token
   - Menjalankan pengecekan otorisasi berbasis peran di setiap RPC
   - Memvalidasi input untuk cegah injeksi.

3. Pengurutan sekuens pesan yang harus dipastikan benar agar pengguna tidak membaca pesan yang teracak.

4. `ReceiverStream` memudahkan konversi `tokio::mpsc::Receiver` menjadi Stream untuk respons gRPC sehingga memisahkan produsen dan gRPC. Namun bisa menyebabkan penumpukan buffer jika konsumen lambat, serta menambah lapisan yang mempersulit penanganan error dan back-pressure dibanding implementasi `Stream` langsung.

5. Pisahkan kode hasil proto, implementasi service, dan logika bisnis ke crate atau modul berbeda. Definisikan trait untuk domain inti, gunakan dependency injection untuk repository dan client, serta centralize interceptor untuk cross-cutting concerns sehingga memperkuat testability dan memudahkan penambahan RPC baru.

6. Validasi dan sanitasi input yang lebih ketat, misalnya memeriksa saldo, batas transaksi, dan format data.Selain itu juga mengimplementasikan mekanisme idempotency (menggunakan idempotency_key) agar panggilan ulang tidak menggandakan transaksi. Dan memastikan pengguna yang melakukan transaksi memiliki hak akses.

7. gRPC mendorong definisi layanan berbasis skema yang ketat, ketergantungan pada Protocol Buffers, dan penggunaan HTTP/2 multiplexing; meningkatkan performa dan interoperabilitas lintas bahasa namun memerlukan gateway atau adapter untuk berkomunikasi dengan sistem REST atau message queue lama.

8. HTTP/2 dapat membuat lebih dari satu stream dalam sebuah koneksi, dan memiliki performa yang lebih baik keseluruhan. Namun, kompleksitasnya menjadi lebih tinggi dan kurang mendukung environment lama.

9. REST one-to-one request-response mudah dipahami tapi menimbulkan latensi tiap panggilan; gRPC bidirectional streaming menjaga koneksi tetap terbuka untuk pertukaran pesan yang terus-menerus tanpa penghubungan ulang, menghasilkan responsivitas lebih baik untuk dashboard live atau notifikasi dua arah.

10. Protocol Buffers memakai skema ketat, encoding biner ringkas, jaminan versioning, dan codegen lintas bahasa, tapi butuh manajemen IDL di awal. JSON fleksibel dan mudah dipakai di lingkungan dinamis, namun payload lebih besar, struktur kurang terjaga, dan rawan error saat deserialisasi runtime.