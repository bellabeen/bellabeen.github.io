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
</br>
Berikut penjelasannya :
* Sebagai contoh disini apabila user ingin menambahkan data lewat sebuah form maka user tersebut melakukan proses **Create**
* Apabila user ingin menghapus data maka proses tersebut disebut **Delete** , proses baca data dinamakan **READ** dan proses update data dinamakan **Update**

## Steps to Reproduce
Buat terlebih dahulu databasenya, kalian bisa menggunakan query database saya dibawah ini. 
</br>
Bagaimana cara menggunakannya? Akses phpmyadmin via localhost
</br>
{% highlight sql %}
CREATE TABLE `simple-crud-api`.`tbl-akademik` ( `id_akademik` INT NOT NULL AUTO_INCREMENT , `npm` INT NOT NULL , `nama` VARCHAR(25) NOT NULL , `alamat` TEXT NOT NULL , PRIMARY KEY (`id_akademik`)) ENGINE = InnoDB;
{% endhighlight %}

Sekarang coba input data mahasiswa dengan klik *menu Insert* 
</br>
{% gist bellabeen/f0c8523edb2628a551a46e79e05e8ff0 %}
</br>
![alt text](https://raw.githubusercontent.com/bellabeen/bellabeen.github.io/master/assets/images/simple-crud-api/01.png)

