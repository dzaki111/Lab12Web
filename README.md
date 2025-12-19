# Lab12Web



### Penjelasan 

**1. Implementasi Front Controller (Routing)**
Dalam tugas ini, `index.php` di root bertindak sebagai **Front Controller**. Penjelasannya adalah semua permintaan (*request*) dari user dikumpulkan ke satu titik file ini terlebih dahulu. Tugasnya adalah memecah URL menjadi segmen-segmen (modul dan halaman) untuk menentukan file mana yang harus dibuka. Dengan cara ini, struktur folder menjadi lebih rapi karena logika pengaturan halaman tidak tersebar di banyak file.

**2. Konsep Objek Database Tunggal (Singleton-like)**
Poin utama dalam tugas OOP ini adalah instansiasi class database. Objek `$db = new Database();` dibuat di file utama agar koneksi ke MySQL hanya terjadi satu kali di awal. File-file modul di dalamnya tidak perlu membuat koneksi baru, melainkan cukup menggunakan objek yang sudah ada. Ini adalah praktik terbaik dalam OOP untuk menghemat penggunaan memori server.

**3. Mekanisme Keamanan Session dan Proteksi Halaman**
Tugas ini menerapkan sistem keamanan berbasis session. Poin penjelasannya adalah sistem akan memeriksa apakah variabel `$_SESSION['is_login']` sudah bernilai `true`. Jika user mencoba mengakses halaman admin (seperti tambah/ubah artikel) tanpa login, sistem routing akan mencegatnya dan mengarahkan kembali ke halaman login. Variabel `$public_pages` digunakan untuk menentukan halaman mana saja yang boleh dilihat oleh tamu (orang yang belum login).

**4. Verifikasi Password dengan Hash Aman**
Dalam kodingan login, tugas ini menekankan penggunaan `password_verify()`. Penjelasannya adalah sistem tidak menyimpan password dalam bentuk teks biasa di database, melainkan dalam bentuk kode acak (hash). Saat login, fungsi ini akan mencocokkan input user dengan kode hash tersebut. Ini adalah standar keamanan industri yang diajarkan dalam pengembangan web modern.

---

### Kodingan yang Berkaitan dengan Tugas

Berikut adalah potongan kode inti yang mengimplementasikan penjelasan di atas:

#### A. Bagian Routing & Proteksi (di `index.php`)

Kode ini bertugas mengatur siapa yang boleh masuk dan halaman apa yang ditampilkan.

```php
// Menentukan modul dan halaman dari URL
$mod = isset($segments[0]) && $segments[0] != '' ? $segments[0] : 'home';
$page = isset($segments[1]) && $segments[1] != '' ? $segments[1] : 'index';

// Menentukan halaman yang bebas diakses (Tanpa Login)
$public_pages = ['user', 'home']; 

if (!in_array($mod, $public_pages)) {
    // Jika mencoba akses modul selain 'user' atau 'home' dan belum login:
    if (!isset($_SESSION['is_login'])) {
        header('Location: /lab11_php_oop/home/index'); // Tendang ke login
        exit();
    }
}

```

#### B. Bagian Verifikasi Login OOP (di `module/home/index.php`)

Kode ini menggunakan objek database untuk memverifikasi identitas pengguna.

```php
// Mengambil data user berdasarkan username dari database
$sql = "SELECT * FROM users WHERE username = '{$username}' LIMIT 1";
$query = $db->query($sql); // Memakai objek $db dari index utama
$data = $query->fetch_assoc();

// Proses pengecekan password secara OOP
if ($data && password_verify($password, $data['password'])) {
    // Jika benar, buat sesi/tanda pengenal
    $_SESSION['is_login'] = true;
    $_SESSION['username'] = $data['username'];
    $_SESSION['nama'] = $data['nama'];
    
    header('Location: /lab11_php_oop/home/index'); 
    exit;
}

```

#### C. Bagian Tampilan Berdasarkan Status Login

Kodingan ini memisahkan tampilan antara tamu dan admin dalam satu file yang sama.

```php
<?php if (!isset($_SESSION['is_login'])) : ?>
    <form method="POST"> ... </form>
<?php else : ?>
    <h2>Halo, <?= $_SESSION['nama']; ?></h2>
    <a href="/lab11_php_oop/artikel/tambah">Tambah Artikel Baru</a>
<?php endif; ?>

```
# berikut adalah hasilnya kalau di run

<img width="1559" height="684" alt="image" src="https://github.com/user-attachments/assets/1ee93b08-41bc-43ef-bb33-73318d0a180a" />
<img width="1635" height="725" alt="image" src="https://github.com/user-attachments/assets/4156bd0e-3128-4b9d-a155-f959dcf6c0c9" />
<img width="1597" height="824" alt="image" src="https://github.com/user-attachments/assets/fae940c5-1d30-40b0-829f-c7310a397dce" />
