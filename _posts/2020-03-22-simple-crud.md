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
* Apabila user ingin menghapus data maka proses tersebut disebut **Delete** , proses baca data dinamakan **READ** dan proses update data dinamakan **Update**

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

{% gist bellabeen/aeb533ab9d358b94d6b4d3a43e0141d0 %}

Kalau sudah pada folder **lib/** buat file dengan nama DB.php lalu taruh script php dibawaha ini -> save

{% gist bellabeen/f0c8523edb2628a551a46e79e05e8ff0/raw/46baee9396e93b3cad9fc108a4de82f99af965e7/config %}

{% gist bellabeen/f0c8523edb2628a551a46e79e05e8ff0/config %}

{% highlight php %}
<?php
define("DB_HOST","localhost");
define("DB_USER","bellabeen");
define("DB_PASSWORD","kepoajalu");
define("DB_NAME","3074218_latihanarjun");
?>
{% endhighlight %}