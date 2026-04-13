# advprog-modul6

## Commit 1 Reflection Notes

Program ini bekerja sebagai single-threaded web server sederhana dengan `TcpListener` yang bind ke `127.0.0.1:7878`. Setiap koneksi masuk diproses satu per satu pada thread utama, sehingga server belum bisa menangani banyak request secara paralel.

Fungsi `handle_connection` menerima `TcpStream`, lalu membungkusnya dengan `BufReader` agar request dari browser dapat dibaca baris demi baris. Hanya bagian request header yang dikumpulkan, karena pembacaan berhenti saat menemukan baris kosong yang menandai akhir header HTTP.

Saat browser mengakses server, program akan mencetak isi request ke terminal. Dari output tersebut bisa dilihat method HTTP, path, host, user-agent, dan header lain yang dikirim browser, sehingga tahap ini membantu memahami pola komunikasi dasar antara browser dan web server.
