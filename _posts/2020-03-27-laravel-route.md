---
title: "[laravel] Memahami Route"
layout: post
date: '2020-03-27 15:15:56'
headerImage: false
star: true
tag:
- php
- laravel
category: blog
author: bellabeen
---

## Pendahuluan
Setiap request yang datang pada Laravel, akan diarahkan melalui sebuah rute *(route)*. *Route* inilah yang akan menentukan respon/apa yang harus dikerjakan untuk membalas request tersebut. Setiap *route* dibuat/didefinisikan pada file **laravelapp/routes/web.php**. **Ingat file ini** , karena kita akan sering memakai dan memodifikasi file **web.php** ini.

## Isi
![Markdowm Image][1]
<figcaption class="caption">Default Landing Page</figcaption>
Gambar diatas merupakan default landing page Laravel yang terletak pada file **web.php**. Berikut ini adalah route untuk menampilkan halaman *default* Laravel tersebut

{% highlight html %}
Route::get('/', function () {
    return view('welcome');
});
{% endhighlight %}

**Keterangan**
1. Pada pemrograman web, ada dua jenis *request method* yang paling sering dipakai *protocol* HTTP, yaitu **GET** dan **POST**. **GET** biasa dipakai ketika ingin membuka suatu halaman web, **POST** biasanya digunakan ketika ingin mengirim data form. Laravel memiliki *facade class* Route {} digunakan untuk menangani *request* yang datang dari browser. Pada kode diatas , *class* ROute{} digunakan untuk menerima *request* **GET** dari *browser*, yaitu dengan fungsi **get()**: **Route::get();**. Pada *protocol* HTTP tidak hanya *request method* GET dan POST untuk mendapatkan dan menerima resource(data), ada juga PUT/PATCH untuk mengupdate suatu resource, ada DELETE untuk menghapus resource. Berikut ini merupakan fungsi yang bisa kita pakai pada *facade class* Route{}:
Route::get();
Route::post();
Route::put();
Route::patch();
Route::delete();
2. Fungsi **get()** pada class Route{} menerima beberapa *parameter*. Pada code diatas bisa kalian liat pada bagian **/** / slash/garis miring berisi **segmen URL** yang akan dicocokkan polanya. Kita sederhanakan bahwa tanda *slash* tersebut merupakan alamat dari file / folder yang akan di eksekusi.
3. Fungsi dari code **function** disebut juga dengan *anonymous function* atau yang lebih dikenal dengan *clouser*. *Clouser* adalah fungsi yang tidak mempunyai nama seperti fungsi fungsi PHP pada umumnya.
4. Badan Fungsi clouser berisi perintah **return view('welcome');**. Memerintahkan agar laravel menampilkan view/ halaman bernama **welcome**. Fungsi *view()* Laravel adalah perintah untuk menampilkan suatu view. Laravel menganut pola M-V-C, dimana **V** adalah View. Fungsi view() menerima parameter berupa *string* yang merupakan nama file *view* dalam hal ini kita menggunakan file bernama **welcome**. 
File *view* welcome tersebut sebenarnya bernama **welcome.blade.php** dan tersimpan di folder **laravelapp/resources/views/welcome.blade.php**. Template engine bawaan dari laravel dengan tambahan ekstensi **.blade**. Singkatnya semua file/ tampilan UI yang akan ditampilkan kepada user disimpan di folder **resources/views**.

Kita coba ubah homepage pada halaman **welcome.blade.php** dengan code dibawah ini:
{% highlight html %}
Route::get('/', function(){
    return 'Halaman Homepage. <br>
    Selamat belajar laravel';
});
{% endhighlight %}

![Markdowm Image][2]
<figcaption class="caption">Welcome Page</figcaption>

## Penutup
Laravel cukup fleksibel, dapat digunakan untuk memberikan beberapa jenis respon terhadap *request* yang datang. Tidak hanya *view* dan *string* seperti contoh diatas, bahkan kita bisa memberikan respon berupa data JSON, melakukan redirect, bahkan respon untuk mendownload file sudah disediakan. Kita akan bahas pada artikel artikel selanjutnya See You

Sekian pengenalan sederhana mengenai routing. Untuk tutorial selanjutnya kalian bisa akses link dibawah ini
1. [Install Composer]()
2. [Install Laravel]()
3. [Setting Server]()
4. [[laravel] Memahami Route]()

[1]: https://raw.githubusercontent.com/bellabeen/bellabeen.github.io/master/assets/images/tutorial-laravel/routing/0.png
[2]: https://raw.githubusercontent.com/bellabeen/bellabeen.github.io/master/assets/images/tutorial-laravel/routing/1.png
