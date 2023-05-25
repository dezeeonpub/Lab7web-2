# Lab7web-2


## LANGKAH - LANGKAH PRAKTIKUM

## PERSIAPAN
Untuk memulai membuat aplikasi CRUD sederhana, yang perlu dipersiapkan adalah database server menggunakan MySQL. pastikan MySQL server sudah dapat dijalankan melalui XAMPP.

## 1). MEMBUAT DATABASE
![create-database](https://github.com/Herli27052000/Lab11Web/blob/master/img/create-database.png)

**PENJELASAN**

Membuat Database

```mySQL
CREATE DATABASE lab_ci4;
```

## 2). MEMBUAT TABEL
![create-table](https://github.com/Herli27052000/Lab11Web/blob/master/img/create-table.png)

```mySQL
CREATE TABLE artikel (
   id INT(11) auto_increment,
   judul VARCHAR(200) NOT NULL,
   isi TEXT,
   gambar VARCHAR(200),
   status TINYINT(1) DEFAULT 0,
   slug VARCHAR(200),
   PRIMARY KEY(id)
);
```
**PENJELASAN** 

Membuat tabel 

![database-artikel](https://github.com/Herli27052000/Lab11Web/blob/master/img/database-artikel.png)

Database berhasil dibuat

## 3). KONFIGURASI KONEKSI DATABASE
Konfigurasi dapat dilakukan dengan dua cara,yaitu pada file **app/config/database.php** atau menggunakan file **.env**. Pada praktikum ini kita gunakan konfigurasi pada file .env.

![konfigurasi-database](https://github.com/Herli27052000/Lab11Web/blob/master/img/konfigurasi-database.png)

**PENJELASAN**

Hapus tanda **#** pada bagian database seperti di atas di file **.env**

## 4). MEMBUAT MODEL
Selanjutnya membuat Model untuk memproses data Artikel. Buat file baru pada direktori **app/Models** dengan nama **ArtikelModel.php**

![artikel-models](https://github.com/Herli27052000/Lab11Web/blob/master/img/artikel-models.png)

**code ArtikelModel**
```php
<?php

namespace App\Models;

use CodeIgniter\Model;

class ArtikelModel extends Model
{
    protected $table = 'artikel';
    protected $primaryKey = 'id';
    protected $useAutoIncrement = true;
    protected $allowedFields = ['judul', 'isi', 'status', 'slug', 'gambar'];
}
```

## 5). MEMBUAT CONTROLLER
Buat Controller baru dengan nama **Artikel.php** pada direktori **app/Controllers.**

![artikel-Controllers](https://github.com/Herli27052000/Lab11Web/blob/master/img/artikel-controllers.png)

**code Artikel.php**
```php
<?php

namespace App\Controllers;

use App\Models\ArtikelModel;

class Artikel extends BaseController
{
    public function index()
    {
        $title = 'Daftar Artikel';
        $model = new ArtikelModel();
        $artikel = $model->findAll();
        return view('artikel/index', compact('artikel', 'title'));
    }
}
```

## 6). MEMBUAT VIEW
Buat direktori baru dengan nama **artikel** pada direktori **app/views,** kemudian buat file baru dengan nama **index.php.**


**code index.php**
```php
<?= $this->include('template/header'); ?>

<?php if($artikel): foreach($artikel as $row): ?>
<article class="entry">
    <h2><a href="<?= base_url('/artikel/' . $row['slug']);?>"><?=$row['judul']; ?></a></h2>
    <img src="<?= base_url('/gambar/' . $row['gambar']);?>" alt="<?=$row['judul']; ?>">
    <p><?= substr($row['isi'], 0, 200); ?></p>
</article>
<hr class="divider" />
<?php endforeach; else: ?>
<article class="entry">
    <h2>Belum ada data.</h2>
</article>
<?php endif; ?>

<?= $this->include('template/footer'); ?>
```

Kemudian Refresh kembali pada browser maka tampilannya akan seperti  gambar dibawah, belum ada data karena belum melakukan insert pada database. URL: http://localhost:8080/artikel


![localhost-artikel](https://github.com/Herli27052000/Lab11Web/blob/master/img/localhost-artikel.png)

**PENJELASAN**

Belum ada data yang ditampilkan. Kemudian coba tambahkan beberapa data pada database agar dapat ditampilkan datanya.

![insert](https://github.com/Herli27052000/Lab11Web/blob/master/img/insert.png)

**PENJELASAN**

Insert atau tambah data pada database dan tabel artikel kemudian refresh kembali pada browser dan tampilannya akan seperti gambar dibawah.

![setelah-insert](https://github.com/Herli27052000/Lab11Web/blob/master/img/setelah-insert.png)
Tampilan Artikel


## 7). MEMBUAT TAMPILAN DETAIL ARTIKEL
Tampilan pada saat judul berita di klik maka akan diarahkan ke halaman yang berbeda. Tambahkan fungsi baru pada **Controller Artikel** dengan nama **view().**

![code-artikel](https://github.com/Herli27052000/Lab11Web/blob/master/img/code-artikel.png)

**code view() pada artikel.php**
```php
public function view($slug)
    {
        $model = new ArtikelModel();
        $artikel = $model->where(['slug' => $slug])->first();
        
        // Menampilkan error apabila data tidak ada.
        if (!$artikel)
        {
            throw PageNotFoundException::forPageNotFound();
        }
        $title = $artikel['judul'];
        return view('artikel/detail', compact('artikel', 'title'));
    }
```

## 8). MEMBUAT VIEW DETAIL
Buat view baru untuk halaman detail dengan nama **app/views/artikel/detail.php**

![view-detail](https://github.com/Herli27052000/Lab11Web/blob/master/img/view-detail.png)

**code detail.php**
```php
<?= $this->include('template/header'); ?>

<article class="entry">
    <h2><?= $artikel['judul']; ?></h2>
    <img src="<?= base_url('/gambar/' . $artikel['gambar']);?>" alt="<?=$artikel['judul']; ?>">
    <p><?= $artikel['isi']; ?></p>
</article>

<?= $this->include('template/footer'); ?>
```

## 9). MEMBUAT ROUTING UNTUK ARTIKEL DETAIL
Buka Kembali file **app/config/Routes.php** kemudian tambahkan routing untuk artikel detail.

**code routing**
```php
$routes->get('/artikel/(:any)', 'Artikel::view/$1');
```

![routes-artikel](https://github.com/Herli27052000/Lab11Web/blob/master/img/routing-artikel.png)

Kemudian refresh dan klik pada bagian link **Artikel pertama** atau pun **Artikel kedua**

![artikel-pertama](https://github.com/Herli27052000/Lab11Web/blob/master/img/artikel-pertama.png)

Di atas adalah contoh Detail artikel pertama

![artikel-kedua](https://github.com/Herli27052000/Lab11Web/blob/master/img/artikel-kedua.png)

Di atas adalah contoh Detail artikel kedua

## 10). MEMBUAT MENU ADMIN
Menu admin adalah untuk proses CRUD data artikel. Buat method baru pada **Controller Artikel** dengan nama **admin_index().**

![admin_index()](https://github.com/Herli27052000/Lab11Web/blob/master/img/admin_index().png)

**code admin_index()**
```php
public function admin_index()
    {
        $title = 'Daftar Artikel';
        $model = new ArtikelModel();
        $artikel = $model->findAll();
        return view('artikel/admin_index', compact('artikel', 'title'));
    }
```

* Selanjutnya buat view untuk tampilan admin dengan nama **admin_index.php**

![code-admin-index()](https://github.com/Herli27052000/Lab11Web/blob/master/img/code-admin-index().png)

**code admin_index.php**
```php
<?= $this->include('template/admin_header'); ?>

<table class="table">
    <thead>
        <tr>
            <th>ID</th>
            <th>Judul</th>
            <th>Status</th>
            <th>Aksi</th>
        </tr>
    </thead>
    <tbody>
    <?php if($artikel): foreach($artikel as $row): ?>
    <tr>
        <td><?= $row['id']; ?></td>
        <td>
            <b><?= $row['judul']; ?></b>
            <p><small><?= substr($row['isi'], 0, 50); ?></small></p>
        </td>
        <td><?= $row['status']; ?></td>
        <td>
            <a class="btn" href="<?= base_url('/admin/artikel/edit/' .$row['id']);?>">Ubah</a>
            <a class="btn btn-danger" onclick="return confirm('Yakin menghapus data?');" href="<?= base_url('/admin/artikel/delete/' .$row['id']);?>">Hapus</a>
        </td>
    </tr>
    <?php endforeach; else: ?>
    <tr>
        <td colspan="4">Belum ada data.</td>
    </tr>
    <?php endif; ?>
    </tbody>
    <tfoot>
        <tr>
            <th>ID</th>
            <th>Judul</th>
            <th>Status</th>
            <th>Aksi</th>
        </tr>
    </tfoot>
</table>

<?= $this->include('template/admin_footer'); ?>
```

* Tambahkan routing untuk menu admin seperti berikut:
![routes-admin](https://github.com/Herli27052000/Lab11Web/blob/master/img/routes-admin.png)

**code routes admin**
```php
$routes->group('admin', function($routes) {
    $routes->get('artikel', 'Artikel::admin_index');
    $routes->add('artikel/add', 'Artikel::add');
    $routes->add('artikel/edit/(:any)', 'Artikel::edit/$1');
    $routes->get('artikel/delete/(:any)', 'Artikel::delete/$1');
  });
```

* Setelah itu buat **Template header** dan **footer** baru untuk **Halaman Admin**. Buat file baru dengan nama **admin_header.php** pada direktori **app/view/template**

![admin-header](https://github.com/Herli27052000/Lab11Web/blob/master/img/admin_header.png)

**code admin_header.php**
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title><?= $title; ?></title>
    <link rel="stylesheet" href="<?= base_url('/admin.css');?>">
</head>
<body>
    <div id="container">
    <header>
        <h1>Admin Portal Berita</h1>
    </header>
    <nav>
    <a href="<?= base_url('/admin/artikel');?>" class="active">Dashboard</a>
        <a href="<?= base_url('/artikel');?>">Artikel</a>
        <a href="<?= base_url('/admin/artikel/add');?>">Tambah Artikel</a>
    </nav>
    <section id="wrapper">
        <section id="main">
```

* Dan buat file baru lagi dengan nama **admin_footer.php** pada direktori **app/views/template** 

![admin-footer](https://github.com/Herli27052000/Lab11Web/blob/master/img/admin_footer.png)

**code admin_footer.php**
```html
<footer>
        <p>&copy; 2022 - Universitas Pelita Bangsa</p>
    </footer>
    </div>
</body>
</html>
```

* Kemudian buat file baru lagi dengan nama **admin.css** pada direktori **ci4/public** untuk mempercantik tampilan **Halaman admin.**

![admin-css](https://github.com/Herli27052000/Lab11Web/blob/master/img/admin-css.png)

* Akses menu admin dengan URL: http://localhost:8080/admin/artikel

Maka tampilannya akan seperti gambar dibawah

![admin-view](https://github.com/Herli27052000/Lab11Web/blob/master/img/admin-view.png)

## 11). MENAMBAHKAN DATA ARTIKEL
Tambahkan fungsi/method baru pada **Controllers Artikel** dengan nama **add().**

![function-add](https://github.com/Herli27052000/Lab11Web/blob/master/img/function-add.png)
**code function add**
```php
public function add()
    {
        // validasi data.
        $validation = \Config\Services::validation();
        $validation->setRules(['judul' => 'required']);
        $isDataValid = $validation->withRequest($this->request)->run();
        
        if ($isDataValid)
        {
            $artikel = new ArtikelModel();
            $artikel->insert([
                'judul' => $this->request->getPost('judul'),
                'isi' => $this->request->getPost('isi'),
                'slug' => url_title($this->request->getPost('judul')),]);
            return redirect('admin/artikel');
        }
        $title = "Tambah Artikel";
        return view('artikel/form_add', compact('title'));
    }
```

* Kemudian buat view untuk form tambah dengan nama **form_add.php** 

![form-add](https://github.com/Herli27052000/Lab11Web/blob/master/img/form_add.png)

**code form_add.php**
```html
<?= $this->include('template/admin_header'); ?>

<h2><?= $title; ?></h2>
<form action="" method="post">
    <p>
        <input type="text" name="judul">
    </p>
    <p>
        <textarea name="isi" cols="50" rows="10"></textarea>
    </p>
    <p><input type="submit" value="Kirim" class="btn btn-large"></p>
</form>

<?= $this->include('template/admin_footer'); ?>
```


## 12). MENGUBAH DATA
Tambahkan fungsi/method baru pada **Controllers Artikel** dengan nama **edit().**

![function-edit](https://github.com/Herli27052000/Lab11Web/blob/master/img/function-edit.png)

**code function edit**
```php
public function edit($id)
    {
        $artikel = new ArtikelModel();

        // validasi data.
        $validation = \Config\Services::validation();
        $validation->setRules(['judul' => 'required']);
        $isDataValid = $validation->withRequest($this->request)->run();
        
        if ($isDataValid)
        {
            $artikel->update($id, [
                'judul' => $this->request->getPost('judul'),
                'isi' => $this->request->getPost('isi'),]);
            return redirect('admin/artikel');
        }

        // ambil data lama
        $data = $artikel->where('id', $id)->first();
        $title = "Edit Artikel";
        return view('artikel/form_edit', compact('title', 'data'));
    }
```

* Kemudian buat view untuk form tambah dengan nama **form_edit.php**

![form-edit](https://github.com/Herli27052000/Lab11Web/blob/master/img/form-edit.png)

**code form_edit.php**
```html
<?= $this->include('template/admin_header'); ?>

<h2><?= $title; ?></h2>
<form action="" method="post">
    <p><input type="text" name="judul" value="<?= $data['judul'];?>" ></p>
    <p><textarea name="isi" cols="50" rows="10"><?=$data['isi'];?></textarea></p>
    <p><input type="submit" value="Kirim" class="btn btn-large"></p>
</form>

<?= $this->include('template/admin_footer'); ?>
```

* Kemudian klik **ubah** pada salah satu artikel 

![edit-artikel1](https://github.com/Herli27052000/Lab11Web/blob/master/img/edit-artikel1.png)

Di atas adalah contoh **ubah/edit** ***artikel pertama***

Sementara dibawah adalah contoh **ubah/edit** ***artikel kedua*** 

![edit-artikel2](https://github.com/Herli27052000/Lab11Web/blob/master/img/edit-artikel2.png)

## 13). MENGHAPUS DATA
Tambahkan fungsi/method baru pada **Controllers Artikel** dengan nama **delete().**

![function-delete()](https://github.com/Herli27052000/Lab11Web/blob/master/img/function-delete.png)

**PENJELASAN**

Untuk hapus klik saja **hapus** maka artikel akan terhapus

**code function delete**
```php
public function delete($id)
    {
        $artikel = new ArtikelModel();
        $artikel->delete($id);
        return redirect('admin/artikel');
    }
```
