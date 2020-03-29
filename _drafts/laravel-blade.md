---
title: "[laravel] Blade (Template Engine)"
layout: post
date: '2020-03-30 15:15:56'
headerImage: false
star: true
tag:
- php
- laravel
category: blog
author: bellabeen
---
## Pendahuluan
Jika sebelumnya kalian sudah membuat view sebagai respon menampilkan data. Sekarang saatnya untuk mengenal Blade. **Blade adalah *Template Engine* Laravel**. Pada dasarnya, blade adalah view, namun blade dengan blade kita memiliki sintak sintak tambahan yang dapat membantu kita menampilkan data. Dan yang paling menarik adalah fitur *Template Inheritance*. 

## Isi
Bagaimana sih menggunakan **blade?** cukup menambahkan ekstensi **.blade.php** pada file yang digunakan
Pada folder siswa , rename file **index.php** menjadi **index.blade.php**. Sangat mudah kan menggunakan file blade, lalu lakukan hal yang sama dengan file homepage dan about menjadi **about.blade.php** dan **homepage.blade.php**

Sejauh ini, kita sudah memiliki 3 buat route, seperti terlihat berikut ini:
{% highlight html %}
Route::get('/', function () {
    return view('pages/homepage');
});

Route::get('about', function(){
    return view('pages/about');
});

Route::get('/siswa', function(){
    $siswa = ['Arba Nugraha', 'Riki Amudra', 'Mahsa Vania Salsabila'];
    return view('siswa.index', compact('siswa'));
});
{% endhighlight %}

Untuk setiap route diatas, kita memberikan respon berupa view. Pada aplikasi sesungguhnya, biasanya ada sebuah file view yang berfungsi sebagai master template. Jadi, file view ini berfungsi untuk mengatur layout dari aplikasi yang kita buat. Di file template tersebut, kita bisa memanggil semua resource yang diperlukan , misalnya file style.css, skrip javascript, bahkan bisa memanggil framework UI seperti boostrap.

Untuk membuat master template, buat file bernama **template.blade.php** kemudian simpan di **laravelapp/resources/views** . Ikuti code dibawah ini:
{% highlight html %}
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>LaravelApp</title>
</head>
<body>
    @yield('main')
    
    @yield('footer')
</body>
</html>
{% endhighlight %}

**Keterangan**
Perintah **@yield** adalah perintah blade. Gunanya untuk menandai bahwa pada bagian itu akan ditempati potongan kode yang biasa disebuat **section**. Dalam hal ini nama section tersebut adalah **main** dan **footer**. Kita akan membuat section main pada tutorial selanjutnya.

Untuk selanjutnya kita membuat **Child Page**. Child Page adalah **Child Template** atau template anak untuk masing - masing view. Gunanya agar master template yang sudah kita buat sebelumnya bisa berfungsi.

Ikuti langkah berikut untuk membuat child template untuk masing masing route yang kita buat.
Pada file **laravelapp/pages/homepage.blade.php** ubah isinya menjadi:
{% highlight html %}
@extends('template')

@section('main')
<div id="homepage">
<h2>Homepage</h2>
<p>Selamat belajar laravel!</p>
</div>
@endsection

@section('footer')
<div id='footer'>
    <p>&copy; 2020 laravelapp.dev</p>
</div>
@endsection
{% endhighlight %}

**Keterangann**
1. File **homepage.blade.php** berfungsi sebagai *child-page* yang mengextends file **template.blade.php**. Jadi nanti file ini memanfaatkan **template.blade.php** sebagai *parent page*. Ini seperti OOP dimana suatu class dapat mengextends class lainnya.
2. Kalian ingat pada file **template.blade.php** terdapat kode **@yield('main)**. Bagian itu akan diisi section **main** pembuatan section bernama main yang akan mengisi bagian tersebut. Begitu pula dengan **section footer** pada master template.

Pada file **laravelapp/pages/about.blade.php** ubah isinya menjadi :
{% highlight html %}
@extends('template')

@section('main')
<div id="about">
<h2>About</h2>
<p>Aplikasi <strong>laravelapp</strong> dibuat sebagai latihan untuk mempelajari Laravel</p>
</div>
@endsection
{% endhighlight %}

Penjelasannya sama dengan sebelumnya. Hanya perlu diperhatikan pada **about.blade.php** kita tidak mendefinisikan section **footer**

Pada file **laravelapp/siswa/index.blade.php** ubah isinya menjadi :
{% highlight html %}
@extends('template')

@section('main')
<div id="siswa">
<h2>Siswa</h2>
<?php if(!empty($siswa)): ?>
    <ul>
        <?php foreach($siswa as $anak): ?>
            <li><?= $anak ?></li>
        <?php endforeach ?>
    </ul>
        <?php else: ?>
        <p>Tidak ada data siswa</p>
<?php endif ?>
</div>
@endsection

@section('footer')
    <div id="footer">
            <p>&copy; 2020 laravelapp.dev</p>
    </div>
@endsection

{% endhighlight %}

**Keterangan**
1. File homepage.blade.php berfungsi sebagai *child page* yang mengextends file *template.blade.php*. Jadi nanti kita akan memanfaatkan **template.blade.php** sebagai *parent page*. Ini seperti OOP dimana suatu class dapat mengextend class lainnya.
2. Kalian ingat pada template.blade.php terdapat kode *@yield('main')*. Bagian itu akan diisi section bernama **main**

Pada browser kalian bisa akses halaman **pages/homepage** akan menampilkan
![Markdowm Image][1]
<figcaption class="caption">Halaman Homepage</figcaption>

Jika kalian mengakses halaman **siswa** akan menampilkan
![Markdowm Image][2]
<figcaption class="caption">Halaman Siswa</figcaption>

Dan untuk halaman about hampir sama dengan halaman homepage dan siswa untuk penjelasannya

## Penutup
Mungkin itu sedikit pendahuluan menggunakan blade engine untuk selanjutnya kita akan bahas mempercantik halaman blade dengan boostrap dll.See you ditutorial selanjutnya.

[1]: https://raw.githubusercontent.com/bellabeen/bellabeen.github.io/master/assets/images/tutorial-laravel/blade/1.png
[2]: https://raw.githubusercontent.com/bellabeen/bellabeen.github.io/master/assets/images/tutorial-laravel/blade/2.png