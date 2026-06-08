# Analisis Perbaikan Responsi

**Nama:** Kaira Meilasya Nayada (Nayya)  
**NIM:** [Isi NIM Kamu di Sini]  
**Kelas:** Infrastruktur Teknologi Informasi  

---

## Permasalahan 1: Syntax Error pada Services

### Gejala
Saat menjalankan perintah `docker compose up -d`, muncul error berupa `yaml: line 3: could not find expected ':'` atau kesalahan parsing block mapping, sehingga kontainer gagal dibangun.

### Penyebab
Terdapat kesalahan penulisan (sintaks) pada file `docker-compose.yml` di baris ke-3, di mana kata kunci `services` ditulis tanpa diikuti tanda titik dua (`:`), yang merupakan standar wajib format file YAML.

### Solusi
Mengubah tulisan `services` menjadi `services:` dan memastikan struktur indentasi (spasi) di bawahnya sudah sejajar dan rapi.

---

## Permasalahan 2: Kesalahan DB_HOST pada Web1

### Gejala
Kontainer `web1` tidak dapat terhubung ke database MySQL meskipun kontainer database sudah aktif.

### Penyebab
Nilai environment `DB_HOST` pada konfigurasi `web1` diisi dengan `mysql`. Padahal, nama service container database yang dideklarasikan di dalam file docker-compose tersebut adalah `db`.

### Solusi
Mengubah baris `DB_HOST: mysql` menjadi `DB_HOST: db` agar aplikasi pada `web1` dapat mengenali jalur jaringan menuju container database yang benar.

---

## Permasalahan 3: Kesalahan Password Database pada Web2

### Gejala
Kontainer `web2` mengalami error *Access Denied* atau gagal melakukan autentikasi saat mencoba membaca data dari database.

### Penyebab
Nilai environment `DB_PASS` pada konfigurasi `web2` diisi dengan `wrongpassword`, yang mana tidak cocok dengan password root/user yang telah diatur pada konfigurasi container `db` (yaitu `student123`).

### Solusi
Mengubah baris `DB_PASS: wrongpassword` menjadi `DB_PASS: student123` agar kredensialnya sinkron dan diizinkan mengakses database.

---

## Permasalahan 4: Typo Nama Folder Context pada Web3

### Gejala
Proses build Docker Compose langsung berhenti dan mengeluarkan error `unable to prepare context: path ".../web33" not found`.

### Penyebab
Pada bagian build `web3`, baris `context` diarahkan ke `./web33`. Sementara itu, nama folder asli yang disediakan di dalam direktori tugas hanya bernama `web3` (menggunakan satu angka 3).

### Solusi
Memperbaiki jalur context pada service `web3` dari `./web33` menjadi `./web3`.

---

## Permasalahan 5: Isolasi Jaringan (Network) pada Web3

### Gejala
Kontainer `web3` tidak bisa diakses dari luar atau tidak menerima beban trafik dari Load Balancer Nginx, meskipun status kontainernya *Running*.

### Penyebab
Pada konfigurasi `networks`, `web3` hanya didaftarkan ke jaringan `- backend`. Akibatnya, container `nginx` (yang bertindak sebagai frontend/load balancer) tidak memiliki jalur komunikasi langsung ke `web3`.

### Solusi
Menambahkan baris `- frontend` di dalam konfigurasi `networks` milik `web3` agar kontainer tersebut tergabung ke dalam dua jaringan sekaligus (frontend dan backend) sama seperti `web1` dan `web2`.

---

## Permasalahan 6: Ketidaksesuaian Nama Volume Persistent

### Gejala
Terjadi error saat inisialisasi volume atau data database tidak tersimpan secara permanen (*persistent*) karena penamaan yang tidak valid saat divalidasi oleh Docker Compose.

### Penyebab
Di bagian konfigurasi container `db`, volume yang dipetakan bernama `- db-data:/var/lib/mysql`. Namun, pada deklarasi blok `volumes:` di bagian paling bawah file, namanya ditulis sebagai `database-data:`. Ketidakcocokan nama ini membuat Docker mendeteksi adanya volume yang belum dideklarasikan.

### Solusi
Mengubah nama deklarasi volume di bagian paling bawah dari `database-data:` menjadi `db-data:` agar selaras dengan nama volume yang digunakan oleh container database.
