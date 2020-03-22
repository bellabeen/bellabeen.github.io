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

## Steps to Reproduce
Buat terlebih dahulu databasenya, kalian bisa menggunakan query database saya dibawah ini. 

Bagaimana cara menggunakannya? Akses phpmyadmin via localhost

{% highlight sql %}
CREATE TABLE `simple-crud-api`.`tbl-akademik` ( `id_akademik` INT NOT NULL AUTO_INCREMENT , `npm` INT NOT NULL , `nama` VARCHAR(25) NOT NULL , `alamat` TEXT NOT NULL , PRIMARY KEY (`id_akademik`)) ENGINE = InnoDB;
{% endhighlight %}

Sekarang coba input data mahasiswa dengan klik *menu Insert* 

![Markdowm Image][/image/assets/images/simple-crud-insert-manual.png]