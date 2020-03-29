---
title: "[laravel] Memahami View Data"
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
Dalam aplikasi sesungguhnya, sangat jarang isi dari suatu file view bersifat statis. Umumnya, suatu view dipakai menampilkan data, misalnya data dari database. Mungkin ada baiknya sebelum sampai pada pembahasan database, misalnya data yang akan kita tampilkan kita buat secara manual.

## Isi
Buat lah route untuk menampilkan daftar siswa:
{% highlight html %}
Route::get('/siswa', function(){
    $siswa = ['Arba Nugraha', 'Riki Amudra', 'Mahsa Vania Salsabila'];
    return view('siswa.index', compact('siswa'));
});
{% endhighlight %}

**Keterangan**
1. Untuk sementara kita menampilkan data berupa data manual berupa array yang kita simpan divariable **$siswa**
2. File view yang kita panggil bernama **index.php** , yang kita simpan di subfolder siswa. Untuk menampilkannya kita menggunakan parameter *GET* kita menggunakan fungsi **compact** untuk melewatkan data kepada view.

Buat sebuah file bernama **index.php**, kemudian simpan di folder **laravelapp/resources/views/siswa/**
{% highlight html %}
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
{% endhighlight %}

Buka browser dan akses folder siswa maka tampilannya akan menjadi seperti gambar dibawah
![Markdowm Image][1]
<figcaption class="caption">Halaman Siswa</figcaption>

**Keterangan**
1. Pertama kita periksa terlebih dahulu apakah variable **$siswa** berisi data siswa dengan menggunakan **if(!empty($siswa))**
2. Jika variable $siswa berisis data maka fungsi perulangan **foreach** kita menampilkan data siswa berupa data list.
3. Jika Variable $siswa kosong, maka akan ditampilkan pesan **Tidak ada data siswa**

## Penutup
Mungkin itu sedikit menampilkan data diview, ada banyak cara nya tidak hanya menggunakan **compact** bisa menggunakan **with, [] / array** . Dari beberapa cara yang bisa dipakai untuk memberikan data kepada view disini saya menggunakan compact. Kemudian **gunakan cara itu secara konsisten agar kode yang kalian tulis menjadi teratur**. See you ditutorial selanjutnya.

[1]: https://raw.githubusercontent.com/bellabeen/bellabeen.github.io/master/assets/images/tutorial-laravel/view/0.png
