---
title: "Simple API CRUD PHP"
layout: post
date: '2020-03-22 18:15:56'
headerImage: false
star: true
tag:
- php
- mysql
category: blog
author: bellabeen
---

## Pendahuluan
**CRUD** merupakan kepanjangan dari Create Read Update Delete yang mana dari keempat proses tersebut merupakan fundamental dari proses logic dari programming.

Berikut penjelasannya :
* Sebagai contoh disini apabila user ingin menambahkan data lewat sebuah form maka user tersebut melakukan proses **Create**
* Apabila user ingin menghapus data maka proses tersebut disebut **Delete** , proses baca data dinamakan **Read** dan proses update data dinamakan **Update**

## Steps to Produce
### Membuat Database
Buat terlebih dahulu databasenya, kalian bisa menggunakan query database saya dibawah ini.

{% gist bellabeen/f0c8523edb2628a551a46e79e05e8ff0 %}
Sekarang coba input data mahasiswa dengan klik *menu Insert* 

![alt text](https://raw.githubusercontent.com/bellabeen/bellabeen.github.io/master/assets/images/simple-crud-api/01.png)

### Koneksi Database PHP dengan MYSQLi
Buat project baru kalian disini saya membuat folder project bernama **simple-crud-api** lalu didalam folder project tersebut buat 2 folder
* conf/
* lib/

Didalam folder **conf/** kalian buat file dengan nama config.php lalu taruh script php dibawah ini -> save

{% highlight sql %}
CREATE TABLE `simple-crud-api`.`tbl-akademik` ( `id_akademik` INT NOT NULL AUTO_INCREMENT , `npm` INT NOT NULL , `nama` VARCHAR(25) NOT NULL , `alamat` TEXT NOT NULL , PRIMARY KEY (`id_akademik`)) ENGINE = InnoDB;
{% endhighlight %}

Kalau sudah pada folder **lib/** buat file dengan nama DB.php lalu taruh script php dibawaha ini -> save

{% highlight php %}
<?php
define("DB_HOST","localhost"); //nama server database
define("DB_USER","bellabeen"); //username database
define("DB_PASSWORD","kepoajalu"); //password database
define("DB_NAME","3074218_latihanarjun"); //nama database yang digunakan
?>
{% endhighlight %}

Untuk memastikan sudah terhubung atau belum, coba kalian akses file di **lib/DB.php** apabila hanya tampilan warna putih saja dilayar berarti koneksi antara PHP dengan MYSQL sudah berhasil. Jika menghasilkan pesan error **Gagal melakukan koneksi ke MYSQL** kondisi tersebut php dengan database masih belum terhubung pastikan dan teliti kembali.