---
layout: post
title:  "DIY Engine Control Unit ROM Reflashing (di CBR250RR)"
date:   2021-12-07 22:00:00 +0700
category: id
author: Kelvin Valencio
description: Reflashing ECU CBR250RR MC51 K64
---
![Reflash CBR250RR](/assets/images/remap-cbr250rr-sendiri-winols.jpg)  

ECU - komputer pada kendaraan injeksi yang mengolah berbagai input dan keadaan kendaraan, dan menentukan perilaku mesin, dan komponen elektronik kendaraan lainnya.

Reflashing ECU merupakan proses mengubah parameter pada kendaraan yang sudah ditentukan pabrik, biasanya dilakukan demi mendongkrak performa. Bagaimana? Dengan mengubah sudut pengapian, sudut & durasi injektor, rev limiter, dan lainnya yang sudah ditentukan dari pabrik.

Reflash & remap biasanya dikenakan 700 ribu hingga 1 juta rupiah, mahalnya jasa ini serta tumbuhnya rasa ingin tahu saya dengan proses dan apa saja yang bisa dimodifikasi dari ECU standar ini membuat saya terjun ke topik ini dan melakukan pendalaman selama beberapa bulan.

Karena kepuasan saya melakukan DIY remap, akan saya bahas di postingan ini, gambaran besar tentang reflashing/remapping hingga langkah-langkah persisnya khusus untuk model motor ini.

## **Peringatan**
- Pastikan model motor kalian CBR250RR dengan kode ECU 38770-K64-N04. Beda motor/model mungkin langkahnya berbeda
- Saya tidak bertanggung jawab atas konsekuensi dari percobaan yang kalian lakukan. Postingan ini ditujukan untuk panduan

## **Tools**
<img src="/assets/images/remap-cbr250rr-sendiri-alat-remap.jpg" alt="Remap CBR250RR sendiri: alat remap">
Berikut hardware dan software yang perlu disiapkan. 

| Nama | Harga | Link
| --- | -: | ---
| write hardware/Alat Remap (K-Line Flash Tool) | Rp ~250.000 |
| File Binary (`BIN`) dan Definition (`XDF`) untuk CBR250RR | *Free* | <a href="https://github.com/kelvinvalencio/cbr250rr-ecu-binary-definition/releases" target="_blank">Download Disini</a>
| Write Software (HondaECU 3.02, 3.5x, atau HondaFlasher) | Rp 2.000.000 | 
| TunerPro Free | *Free* | <a href="https://www.tunerpro.net/downloadApp.htm" target="_blank">Download disini</a>

## **Overview**
### TunerPro, File .bin, dan File .xdf
- `TunerPro` adalah software untuk mengubah pengaturan ECU. `TunerPro` memerlukan `BIN` yang akan di edit, dan `XDF` yang mendefinisikan parameter yang ada di `BIN`.
- `BIN` adalah file binary/raw dari ECU, bisa dibilang merupakan ROM dari ECU. Beda motor tentu beda binary. Didalamnya ada pengaturan seperti:
    - Map bensin
    - Map pengapian
    - Rev Limiter
    - Dan masih banyak lagi
- `XDF` adalah file Definition yang menjelaskan letak alamat pengaturan yang ada di `BIN`. **File XDF beda-beda tiap motor**, karena beda `BIN` tentunya beda `XDF` karena beda letak pengalamatan tiap-tiap pengaturan yang tersimpan di `BIN`.


![Reflash CBR250RR](/assets/images/remap-cbr250rr-binary_patching.jpg)
### TunerPro Definition Building
Salah satu tahap yang *time-consuming*  adalah membuat binary definition, pada kasus ini binary definition yang dibangun adalah XDF atau TunerPro Definition File. Sebagai definition builder, tugas kita adalah menentukan byte range pada ROM/binary ECU, menganalisa berdasarkan pengetahuan, pemahaman tentang pola map, untuk menyimpulkan:
- jenis tabel (fuel map, ignition map, etc.)
- dimensi tabel (10x20? 20x10?)
- axis dari tabel (x=throttle, y=rpm, z=fuel?)

informasi ini kemudian dikemas menjadi XDF pakai TunerPro, sebagai interface untuk Tuner dengan mudahnya memodifikasi nilai-nilai pada ROM ECU, melakukan operasi pada tabel seperti *scale by percent*, *smooth table*, dan *interpolate*.

### File XDF untuk CBR250RR
Saya sempat mencari XDF untuk CBR250RR, namun rentang harganya luar biasa. Ada yang menjual mulai dari 1.5 juta dan keatas.
Khusus untuk CBR250RR 38770-K64-N04, saya sudah membuat definisi lengkap beserta file XDF yang bisa <a href="https://kelvinvalencio.github.io/projects/cbr250rr-ecu-binary-definition/" target="_blank">di-download disini.</a>

### Sebenernya Binary Patching
Jadi ketika kita melakukan reflash, kurang lebih yang kita lakukan adalah <a href="https://student-activity.binus.ac.id/csc/2021/08/binary-patching-menggunakan-ida-pro-part-1/" target="_blank">binary patching atau patching</a>.  
Simpelnya, kita membaca nilai-nilai yang ada di file mentah `BIN` tersebut, lalu kita ubah nilainya, lalu kita kirim kembali ke ECU!

### Checksum
Checksum pada ECU umumnya adalah rentang alamat khusus yang berisi nilai checksum, untuk menentukan integritas dari binary.  
Simpelnya, checksum ini dihitung sedemikian rupa berdasarkan nilai-nilai yang ada di binary. Ketika kita mengubah nilai yang ada di binary ECU, maka checksum sebenarnya **harus dihitung ulang**. Jika tidak, saat ECU melakukan proses **booting** dan menemukan bahwa checksum tidak sesuai dengan angka yang ada di ECU, motor mungkin tidak mau nyala karena dianggap ada perubahan pada memori yang tidak disengaja.

Algoritma checksum ada beragam, beda ECU kadang beda algoritma. Namun write software seperti <a href="https://mcuinnovations.com/software/hondaecu/" target="_blank">HondaECU</a> sudah dapat melakukan **checksum correction** secara sendirinya. Jadi saat binary yang berubah hendak dikirim ke ECU, write software tertentu akan mengubah lagi nilai checksum sesuai dengan kalkulasi yang seharusnya.

### Flashing ke ECU
![Remap CBR250RR sendiri write software/flasher software](/assets/images/remap-cbr250rr-sendiri-write-software.png)  

Nah, kalo sampe sini masih nyambung, kita lanjut ke tahap **flashing** dimana file .bin baru kita kirim ke ECU. Flashing ini memerlukan `write software` dan `write hardware`. Jadi kurang lebih kita menyambungkan `write hardware` ke komputer, lalu kita pakai `write software` untuk "berbicara" dengan ECU lewat `write hardware`. 

Nah tadi gambaran besarnya supaya punya pegangan dengan konsepnya. Sekarang kita masuk ke eksekusinya (ini yang kalian tunggu-tunggu)

## **Execution**
### Persiapan
1. Download driver software yang sesuai
2. Download `BIN` untuk <a href="https://github.com/kelvinvalencio/cbr250rr-ecu-binary-definition/raw/main/CBR250RR-38770-K64-N04_BIN.bin" target="_blank">CBR250RR 38770-K64-N04 disini</a>
3. Download `XDF` untuk <a href="https://github.com/kelvinvalencio/cbr250rr-ecu-binary-definition/releases" target="_blank">CBR250RR 38770-K64-N04 disini</a>. Pilih versi release terbaru, lalu klik bagian **assets**, lalu klik file `.xdf`nya. Di contoh ini kita akan pake versi v0.95 yang bisa langsung <a href="https://github.com/kelvinvalencio/cbr250rr-ecu-binary-definition/releases/download/38770-K64-N04-v0.95/CBR250RR_38770-K64-N04_PUBLIC_v095.xdf" target="_blank">di-download disini</a>
4. Buka TunerPro, open bin dan XDF, lakukan perubahan yang diinginkan pada parameter yang tersedia.

### Mengubah binary/ROM
Jika berhasil maka akan terlihat tampilan seperti dibawah
![Remap CBR250RR sendiri software TunerPro](/assets/images/remap-cbr250rr-sendiri-tunerpro.png)  
Di contoh ini, kita akan mencoba menaikkan RPM Limiter CBR250RR kita dari 14000 menjadi *14500*. Dipersilahkan untuk skip step ubah limiter dan membuat ubahan yang kalian hendaki sendiri
1. Pada Parameter Tree `TunerPro` klik `Limiters`, maka akan terlihat tabel `RPM Limiter 1` hingga `RPM Limiter 5`
2. Buka tabel-tabel tersebut, dan ubah nilainya menjadi `14500` untuk semua kolom.
![Remap CBR250RR sendiri limiter](/assets/images/remap-cbr250rr-sendiri-limiter.png)
3. Save tiap ubahan di window tabel
4. Save ubahan ke binary dengan klik `File` > `Save Bin` atau `Save Bin As`, terserah kalian, tapi ingat file yang diubah

### Flashing ke ECU
<img align="center" height="343" width="493" src="/assets/images/remap-cbr250rr-sendiri-physical-setup.jpg" alt="Belajar remap CBR250RR sendiri">  
Tahap ini waktunya write bin yang udah kalian ubah ke ECU. Saya biasanya menggunakan software HondaECU untuk writing ke ECU
1. Sambungkan alat remap ke PC/laptop
2. Hubungkan kabel DLC ke laptop melalui reflash hardware
3. Browse file bin yang sudah dimodifikasi
4. Atur checksum address, spesifik model kendaraan saya ada di `0x7FFF8`
5. Start flashing dan tunggu sampai selesai

## **Final Thoughts**
DIY Reflashing sudah umum di negeri luar, komunitas dan material belajar yang tersedia cukup banyak, sehingga mendukung individu yang penasaran.  
Harapan saya di Indonesia makin banyak orang dengan rasa ingin tahu lebih tinggi ke bagaimana sesuatu bekerja, termasuk otak kecil pengontrol sistem injeksi ini.  

Udah bisa reflash, sekarang apa? Tahap selanjutnya saya saranin pelajari konsep tuning seperti ignition tuning, fuel tuning. Saya rekomen video-video dari channel Youtube <a href="https://www.youtube.com/watch?v=5KdbEXYD224" target="_blank">HP Academy</a>, yang memberikan pengenalan konsep yang baik tentang tuning mesin injeksi.  
Dari situ kalo kalian udah nyaman dengan teorinya, baru coba mengubah Fuel dan Ignition Map.
