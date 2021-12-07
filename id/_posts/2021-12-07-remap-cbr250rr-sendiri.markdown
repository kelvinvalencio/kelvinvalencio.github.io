---
layout: post
title:  "Berbagi Pengalaman Remapping/Reflashing CBR250RR Sendiri Modal Rp 650.000"
date:   2021-12-07 15:33:32 +0700
category: id
author: Kelvin Valencio
---
![Illustration: WinOLS on CBR250RR N04 Binary](/assets/images/remap-cbr250rr-sendiri-winols.jpg)  

Remapping ECU bawaan motor belakangan ini lagi hot, dan umum banget. Orang-orang memilih remap sebagai cara upgrade performa.
Saya sendiri melakukan DIY remap karena penasaran dengan parameter ECU apa saja yang bisa diubah. Tapi harga remap CBR250RR sendiri kisaran *1 Jutaan* di bengkel-bengkel.  
Jadi saya belajar untuk remap/reflash sendiri. Selain murah, saya punya kebebasan mau remap berapa kali tanpa bayar lagi.  

## **Resiko, Saran, Peringatan**
- Resiko terburuk bisa merusak ECU dan merusak motor
- Saya anjurkan dalam sebelum melaksanakan, pastikan kalian setidaknya kompeten menggunakan komputer
- Pastikan model motor kalian CBR250RR dengan kode ECU 38770-K64-N04. Beda motor/model mungkin punya langkah yang berbeda
- Saya tidak bertanggung jawab atas konsekuensi dari percobaan yang kalian lakukan. Postingan ini ditujukan untuk panduan
- Apabila informasi yang saya sediakan kurang/tidak akurat, yang kalian lakukan terhadap informasi ini bukan tanggung jawab saya, tapi saya menerima *feedback* untuk merevisi jika ada kesalahan

## **Tools**
Berikut hardware dan software yang perlu disiapkan. Saran saya luangin waktu kalian buat cari-cari tool yang dijual dengan teliti supaya mendapatkan harga terbaik dan sesuai. Saya sendiri berhasil mencari kelengkapan alat-alat dengan budget tidak melebihi biaya remap di bengkel-bengkel.

| Nama | Harga | Link
| --- | -: | ---
| Write Hardware/Alat Remap (K-Line Flash Tool) | Rp 450.000 | <a href="https://shopee.co.id/product/277642167/5561381862?smtt=0.197913951-1638877447.9" target="_blank">Shopee</a>
| File Binary (`BIN`) dan Definition (`XDF`) untuk CBR250RR | ~~Rp 1.000.000 - 2.000.000~~ *Free* dari saya | <a href="https://github.com/kelvinvalencio/cbr250rr-ecu-binary-definition/releases" target="_blank">Download Disini</a>
| Write Software (HondaECU 3.02, 3.5x, atau HondaFlasher) | Rp 600.000 - 1.000.000 | <a href="https://www.tokopedia.com/search?st=product&q=honda%20flash&navsource=home" target="_blank">Tokopedia</a>
| | Rp 200.000 | Kontak saya untuk write software 200.000
| TunerPro Free | *Free* | <a href="https://www.tunerpro.net/downloadApp.htm" target="_blank">TunerPro Download Page</a>
| **Total Termurah** | **Rp 650.000** |

## **Overview**
### TunerPro, File .bin, dan File .xdf
- `TunerPro` adalah software untuk mengubah mapping sesuka kalian. `TunerPro` memerlukan `BIN` yang akan di edit, dan `XDF` yang mendefinisikan parameter yang ada di ECU.
- `BIN` adalah file binary/raw dari ECU, bisa dibilang merupakan ROM dari ECU. Didalamnya ada pengaturan seperti:
    - Mapping bensin
    - Mapping pengapian
    - Pengaturan limiter RPM
    - Dan masih banyak lagi
- `XDF` adalah file Definition yang menjelaskan letak alamat mapping yang ada di `BIN`. **File XDF beda-beda tiap motor**, karena beda `BIN` tentunya beda `XDF` karena beda letak pengalamatan tiap-tiap pengaturan yang tersimpan di `BIN`.

### Sebenernya Binary Patching
Jadi ketika kita melakukan remap/reflash, kurang lebih kita melakukan yang disebut <a href="https://student-activity.binus.ac.id/csc/2021/08/binary-patching-menggunakan-ida-pro-part-1/" target="_blank">binary patching atau patching</a>.  
Simpelnya, kita membaca nilai-nilai yang ada di file mentah `BIN` tersebut, lalu kita ubah nilainya, lalu kita kirim kembali ke ECU!

### Tentang Checksum
Checksum pada ECU umumnya adalah rentang alamat khusus yang berisi nilai checksum, untuk menentukan integritas dari binary.  
Simpelnya, checksum ini dihitung sedemikian rupa berdasarkan value yang ada di mapping, dll. Ketika kita mengubah nilai yang ada di binary ECU, maka checksum sebenarnya *harus dihitung ulang*.

Algoritma checksum ada beragam, beda ECU kadang beda algoritma. Namun software write seperti <a href="https://mcuinnovations.com/software/hondaecu/" target="_blank">HondaECU</a> sudah dapat melakukan **checksum correction** secara sendirinya. Jadi saat binary yang sudah diubah hendak dikirim ke ECU, software write tertentu secara diam-diam mengubah lagi nilai checksum sesuai dengan kalkulasi yang sebetulnya.

### Siapa Yang Bikin XDF? Gimana Bikin XDF?
XDF itu dibuat oleh orang biasa, kalian juga bisa bikin XDF sendiri. Untuk kendaraan tertentu, ada XDF yang lengkap, ada XDF yang kurang lengkap.  
Biasanya XDF itu diperjualbelikan oleh orang yang meluangkan waktunya untuk menganalisa file `.bin` kendaraan tertentu.  
Umumnya pembuatan XDF dilakukan dengan menganalisa `.bin` mentah, biasanya menggunakan software seperti <a href="https://www.evc.de/en/product/ols/software/" target="_blank">WinOLS</a>, yang bisa membantu mengelompokkan mapping berdasarkan pola yang ada di file tersebut.

Khusus untuk CBR250RR 38770-K64-N04, saya sudah membuat definisi lengkap beserta file XDF yang bisa <a href="projects/cbr250rr-ecu-binary-definition/" target="_blank">di-download gratis disini.</a>

Untuk cara menganalisa dan membuat XDF sendiri mungkin bakal saya bahas di postingan mendatang.

### Flashing ke ECU
![Remap CBR250RR sendiri write software/flasher software](/assets/images/remap-cbr250rr-sendiri-write-software.png)  

Nah, kalo sampe sini masih nyambung, kita lanjut ke tahap **flashing** dimana kita mengirim file yang sudah kita ubah ke ECU. Flashing ini memerlukan `write software` dan `write hardware`. Jadi kurang lebih kita menyambungkan `write hardware` ke komputer, lalu kita pakai `write software` ini untuk "berbicara" dengan ECU lewat `write hardware`. 

Nah tadi gambaran besarnya supaya punya pegangan dengan konsepnya. Sekarang kita masuk ke eksekusinya (ini yang kalian tunggu-tunggu)

## **Execution**
### Persiapan
1. Download virtual driver zadig, cari versi 2.5 yang <a href="https://zadig.akeo.ie/downloads/" target="_blank">di-download disini</a>. Ingat, ini hanya untuk HondaECU 3.02
2. Download `BIN` untuk <a href="https://github.com/kelvinvalencio/cbr250rr-ecu-binary-definition/raw/main/CBR250RR-38770-K64-N04_BIN.bin" target="_blank">CBR250RR 38770-K64-N04 disini</a>
3. Download `XDF` untuk <a href="https://github.com/kelvinvalencio/cbr250rr-ecu-binary-definition/releases" target="_blank">CBR250RR 38770-K64-N04 disini</a>. Pilih versi release terbaru, lalu klik bagian **assets**, lalu klik file `.xdf`nya. Di contoh ini kita akan pake versi v0.95 yang bisa langsung <a href="https://github.com/kelvinvalencio/cbr250rr-ecu-binary-definition/releases/download/38770-K64-N04-v0.95/CBR250RR_38770-K64-N04_PUBLIC_v095.xdf" target="_blank">di-download disini</a>
4. Buka `TunerPro`
5. Pada menu `TunerPro`, klik `File` > `Open Bin`, lalu pilih file `.bin` yang sudah di-download
6. Pada menu `TunerPro`, klik `XDF` > `Select XDF`, lalu pilih file `.xdf` yang sudah di-download

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
Tahap ini waktunya write bin yang udah kalian ubah ke ECU. Saya biasanya menggunakan HondaECU 3.02 untuk writing ke ECU
1. Sambungkan alat remap ke PC/laptop
2. Buka aplikasi zadig, klik `options` > `show all devices`
3. Pada zadig, pilih device yang sesuai dengan alat remap kalian, umumnya bertuliskan `FT232R USB UART`, lalu pada field driver disamping icon **panah/arrow**, pilih libusbK v3.0.7.0, lalu klik replace driver, hingga muncul dialog box bahwa sudah berhasil
![Remap CBR250RR sendiri zadig](/assets/images/remap-cbr250rr-sendiri-zadig.png)
4. Buka tempat duduk pengendara, didalamnya ada kabel Data Link Connector (DLC).
5. Sambungkan kabel DLC dengan alat remap
6. Nyalakan kontak motor, **pastikan kill switch di keadaan on** (bukan nyalain motor)
7. Buka software write, di contoh ini kita pake HondaECU v3.02
8. Pilih menu `ECU info`, pastikan field `state` tertulis `ECUSTATE.OK` sebelum melanjutkan, jika belum berarti ECU belum terdeteksi.
9. Pilih menu `Write ECU`
10. Pilih file .bin yang sudah diubah
11. Pastikan centang `Fix` pada `Checksum Location`
12. `Start Offset` diisi dengan `0x0`. Karena untuk CBR250RR, flashing dimulai dari awal alamat ROM
13. `Checksum Location` diisi dengan `7FFF8` atau `0x7FFF8`. Ini sangat penting supaya software write dapat melakukan **checksum correction** secara otomatis.
14. Klik **start**
15. Tunggu sekitar 20 menit hingga writing selesai

## **Final Thoughts**
DIY Reflashing sudah umum di negeri luar, komunitas dan material belajar yang tersedia cukup banyak, sehingga mendukung individu yang penasaran.  
Harapan saya di Indonesia makin banyak orang dengan rasa ingin tahu lebih tinggi ke bagaimana sesuatu bekerja, termasuk otak kecil pengontrol sistem injeksi ini.