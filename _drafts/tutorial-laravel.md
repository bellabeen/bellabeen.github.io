---
title: "Tutorial Laravel"
layout: post
date: '2020-03-25 09:15:56'
headerImage: false
star: true
tag:
- php
- mysql
- laravel
- routing
category: blog
author: bellabeen
---

## Pendahuluan
Hi, selamat pagi pieps. Apakabarnya


{% highlight sql %}
CREATE TABLE `simple-crud-api`.`tbl-akademik` ( `id_akademik` INT NOT NULL AUTO_INCREMENT , `npm` INT NOT NULL , `nama` VARCHAR(25) NOT NULL , `alamat` TEXT NOT NULL , PRIMARY KEY (`id_akademik`)) ENGINE = InnoDB;
{% endhighlight %}

Sekarang coba input data mahasiswa dengan klik *menu Insert* 

![alt text](https://raw.githubusercontent.com/bellabeen/bellabeen.github.io/master/assets/images/simple-crud-api/01.png)

![Markdowm Image][1]
<figcaption class="caption">Hello World</figcaption>

### Koneksi Database PHP dengan MYSQLi
Buat project baru kalian disini saya membuat folder project bernama **simple-crud-api** lalu didalam folder project tersebut buat 2 folder
* conf/
* lib/


Kalau sudah pada folder **lib/** buat file dengan nama DB.php lalu taruh script php dibawaha ini -> save

{% highlight php %}
test
{% endhighlight %}

Sekian tutorial sederhana membuat koneksi php dengan database. Untuk tutorial selanjutnya kalian bisa akses link dibawah ini
1. [Install Composer]()
2. [Install Laravel]()
3. [Setting Server]()
4. [Routing]()


[1]: https://raw.githubusercontent.com/bellabeen/bellabeen.github.io/master/assets/images/simple-crud-api/01.png