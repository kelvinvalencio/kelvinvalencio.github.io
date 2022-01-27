---
layout: post
title: "Duplikat Kartu RFID Sendiri Menggunakan Arduino"
date: 2022-01-27 22:00:00 +0700
category: id
author: "Kelvin Valencio"
description: "Cara duplikat kartu RFID kartu absen/member menggunakan Arduino Uno R3"
---

![Duplikat kartu RFID menggunakan Arduino](/assets/images/duplikat-kartu-rfid-menggunakan-arduino.jpg)  

Penerapan RFID udah luas banget. Contohnya member parkir, absen di kampus, absen di kantor, kunci pintu rumah, dan masih banyak lagi.  
Setelah belajar lebih lanjut soal RFID, ternyata kartu RFID bisa diclone jika menggunakan kartu yang <a href="https://www.electron.id/article/rfid-dengan-frekuensi-13-56mhz-nfc-iso14443.html#mifare-di-indonesia" target="_blank">UID-rewritable</a>.

Tinggal di salah satu lingkungan yang sudah menerapkan sistem keluar masuk menggunakan kartu seperti ini, membuat saya termotivasi untuk belajar duplikat kartu RFID. Pasalnya, saya sering menghilangkan kartu seperti ini, dan jika kehilangan kartu member ini umumnya **dikenakan denda** oleh pihak layanan sistem ini.

Denda ini umumnya mulai dari Rp 50.000. Padahal jelas harga kartu RFID yang <a href="https://www.electron.id/article/rfid-dengan-frekuensi-13-56mhz-nfc-iso14443.html#mifare-di-indonesia" target="_blank">UID-rewritable</a> tidak mencapai 8 ribu. Faktanya, saya membeli kartu duplikat ini dengan harga 7 ribu rupiah.

Keputusan saya untuk menggunakan Arduino berdasarkan pertimbangan bahwa:
* Untuk memiliki hands-on experience terhadap Arduino
* Budget saya tidak mencapai/melebihi alat clone yang berkisar 120 ribu rupiah atau lebih

Kebanyakan HP yang mendukung NFC bisa digunakan untuk duplikat, tapi perlu kartu khusus yang mendukung aplikasi seperti <a href="https://play.google.com/store/apps/details?id=de.syss.MifareClassicTool" target="_blank">Mifare Classic Tool di Android</a>. Jadi kartu yang bisa ditulis dengan modul Arduino ini belum tentu bisa ditulis ulang menggunakan aplikasi tersebut.  

Pada post ini saya hanya membahas duplikat menggunakan Arduino. Untuk duplikat menggunakan HP NFC mungkin akan saya bahas di lain post.

## **Tools**
<img src="/assets/images/duplikat-kartu-rfid-menggunakan-arduino-2.jpg" alt="Duplikat kartu RFID menggunakan Arduino">
Berikut peralatan yang saya gunakan beserta harganya. Tentunya harga beda-beda tiap lapak, namun saya cukup puas dengan belanjaan dibawah:

| Nama | Harga | Link
| --- | -: | ---
| Arduino Uno R3 Clone | Rp 65.000 | <a href="https://www.tokopedia.com/toko-tekno/arduino-uno-r3-clone" target="_blank">Tokopedia</a>
| Modul Arduino: RFID RC522 | Rp 3.250 | <a href="https://www.tokopedia.com/tekno-kreasi-elektronik/sensor-rfid-rc522-module-rfid-module-rfid-card" target="_blank">Tokopedia</a>
| Solder 40w | 8.800 | <a href="https://www.tokopedia.com/zencarparts/alat-solder-listrik-patri-listrik-timah-soldering-iron-40w-40watt-220v" target="_blank">Tokopedia</a>
| Timah untuk solder | 11.900 | <a href="https://www.tokopedia.com/meinaelektrik/welsolo-vvs-633a-timah-gulung-solder-timah-soldering-original-0-3mm" target="_blank">Tokopedia</a>
| Breadboard 830 | Rp 11.600 | <a href="https://www.tokopedia.com/mirorim/mb-102-breadboard-830-holes-lubang-solderless-for-prototyping-arduino" target="_blank">Tokopedia</a>
| Kabel Jumper Arduino Male to Male minimal 7 pcs | Rp 5.800 | <a href="https://www.tokopedia.com/roboware/kabel-jumper-pelangi-arduino-20cm-male-to-male-20-pcs-breadboard" target="_blank">Tokopedia</a>
| Kartu Mifare Classic 1K 13.57MHz Rewritable UID | Rp 7.000 | <a href="https://www.tokopedia.com/electron/13-56-mhz-rewritable-uid-card-writable-rewritable-clone-duplicate" target="_blank">Tokopedia</a>
| **Total** | **Rp 113.350** |

Solder dan timah digunakan untuk melakukan solder pin header modul RFID dengan board RFIDnya. Sebaiknya punya solder, tapi saya sendiri pertama kali mengoperasikan solder saat membuat project ini.  
Breadboard dan kabel jumper digunakan untuk menghubungkan modul RFID dengan Arduino.  
Kartu RFID harus tipe Mifare Classic 1K UID Rewritable, karena merupakan kartu yang paling umum digunakan. Ingat untuk tidak menggunakan kartu yang didapatkan dari paket modul RC522, karena kartu-kartu tersebut hanya UID read-only.

## Overview
<img src="/assets/images/duplikat-kartu-rfid-menggunakan-arduino-3.jpg" alt="Duplikat kartu RFID menggunakan Arduino">
Kartu RFID itu ada banyak model dan kadang tidak kompatibel antara satu sama lain. Kebanyakan kartu yang digunakan untuk kartu absen, kartu parkir, kartu member, dll. adalah **NXP Mifare Classic 1K** dengan standar ISO 14443 Type A, yang bekerja di frekuensi tinggi (13.57 MHz).  
Kartu ini menampung data 1024 byte, terdiri dari 16 sektor memiliki UID atau Unique Identifier pada **4 byte** pertama di sektor 0 dari block memori kartu ini. Kartu Mifare 1K yang mendukung UID-rewritable umumnya hanya bisa tulis ulang pada sektor 0.

Ketika melakukan duplikat kartu, yang biasanya perlu dicapai adalah menduplikat 4 byte pertama dari sektor 0 pada kartu RFID. Jadi yang akan saya bahas di post ini adalah memasang Arduino dengan modulnya, lalu menggunakan example library dari Arduino IDE untuk membaca UID kartu yang mau diduplikat, lalu menulis UID pada kartu duplikat.

## **Execution**
### Persiapan
<img src="/assets/images/duplikat-kartu-rfid-menggunakan-arduino-6.png" alt="Duplikat kartu RFID menggunakan Arduino">
Pada tahap persiapan, saya mendownload Arduino IDE yang bisa <a href="https://www.arduino.cc/en/software" target="_blank">didownload disini</a>, kemudian mendownload library MFRC522 oleh GithubCommunity yang bisa diinstall melalui menu **Manage Libraries** pada menu **Sketch**.

Setelah itu saya solder pin header dengan modul RFID 522. Pin header include dengan modulnya. Caranya bisa lihat <a href="https://youtu.be/wv5Mp1mn_8M" target="_blank">video ini</a>

### Pemasangan Arduino dengan Modul RC522
Pada tahap pemasangan, Arduino Uno dipasangkan dengan modul RC522 sesuai dengan tabel wiring dibawah:

|Pin|	Wiring ke Arduino Uno
| -- | --- 
|SDA|	Digital 10
|SCK|	Digital 13
|MOSI|	Digital 11
|MISO|	Digital 12
|IRQ|	-
|GND|	GND
|RST|	Digital 9
|3.3V|	3.3V

Berikut skematik dari <a href="https://randomnerdtutorials.com/security-access-using-mfrc522-rfid-reader-with-arduino/" target="_blank">RandomNerdTutorials</a>:
<img src="/assets/images/duplikat-kartu-rfid-menggunakan-arduino-5.png" alt="Duplikat kartu RFID menggunakan Arduino">

Karena menggunakan breadboard, maka tidak persis skematik, karena breadboard akan menjadi perantara hubungan antara Wiring ke Arduino dengan Pin pada modul RC522, jadi akhirnya terlihat seperti ini
<img src="/assets/images/duplikat-kartu-rfid-menggunakan-arduino-4.jpg" alt="Duplikat kartu RFID menggunakan Arduino">

### Cek DumpInfo Kartu Asli/Lama
Setelah koneksi siap, maka saya memastikan bahwa kartu asli yang mau diduplikat adalah model kartu yang cocok dengan kartu duplikat. Caranya adalah dengan mengakses submenu **Examples** pada menu **File** pada Arudino IDE, program yang perlu digunakan adalah **DumpInfo** lalu upload sketch ke Arduino dengan tombol **Upload** hingga muncul pesan  
<img src="/assets/images/duplikat-kartu-rfid-menggunakan-arduino-7.png" alt="Duplikat kartu RFID menggunakan Arduino">

Menu **Serial Monitor** akan menampilkan data kartu ketika kartu didekatkan dengan modul RC522, tapi yang perlu dicatat hanyalah **UID**.  
<img src="/assets/images/duplikat-kartu-rfid-menggunakan-arduino-8.png" alt="Duplikat kartu RFID menggunakan Arduino">

### Proses Duplikat ke Kartu Baru
<img src="/assets/images/duplikat-kartu-rfid-menggunakan-arduino-12.jpg" alt="Duplikat kartu RFID menggunakan Arduino">
Setelah mencatat UID yang perlu dicopy, dalam kasus ini adalah **EC F6 33 49**, maka tinggal mengakses example bernama **ChangeUID**, lalu buat perubahan pada line yang mendefinisikan UID baru, diubah sesuai dengan UID yang diinginkan.  
<img src="/assets/images/duplikat-kartu-rfid-menggunakan-arduino-9.png" alt="Duplikat kartu RFID menggunakan Arduino">  
Menjadi  
<img src="/assets/images/duplikat-kartu-rfid-menggunakan-arduino-10.png" alt="Duplikat kartu RFID menggunakan Arduino">

Setelah upload dan melihat output Serial Monitor, maka ketika mendekatkan **kartu baru** ke modul RC522, maka Arduino akan langsung menulis ulang UID kartu sesuai yang didefinisikan. Hal tersebut bisa dipastikan dengan output seperti dibawah:  
<img src="/assets/images/duplikat-kartu-rfid-menggunakan-arduino-11.png" alt="Duplikat kartu RFID menggunakan Arduino">  
Dari output tersebut, UID kartu yang sebelumnya **7D 53 63 32** menjadi **EC F6 33 49**. Sesuai dengan kartu lama!!

Maka dapat disimpulkan bahwa proses duplikat telah berhasil, kartu baru sudah dapat digunakan sebagai cadangan.

## **Final Thoughts**
Melakukan duplikat kartu RFID menggunakan Arduino memberikan pengalaman hands-on terhadap Arduino dan modul RFID nya, memperluas pengetahuan saya tentang teknologi RFID, jenis-jenis yang tersedia, serta membantu saya mengenali berbagai macam tipe RFID dari frekuensi, struktur memori, serta model yang berbeda.

Selain itu saya jadi bisa menghemat uang dengan menyiapkan kartu duplikat, yang bisa mencegah saya harus membayar denda kehilangan kartu seperti ini yang nilai dendanya tidak fair.
