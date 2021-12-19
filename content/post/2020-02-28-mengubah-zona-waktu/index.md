---
title: Mengubah Zona Waktu Pada Data Pengamatan Pasut
date: 2020-02-28
slug: mengubah-zona-waktu-data-pasut
tags: 
- Pasut
- Matlab
categories : 
- Hidrografi
typora-root-url: ./
---

Data pengamatan pasang surut laut sekarang ini banyak yang sudah bisa didownload secara bebas, beberapa diantaranya melalui web [IOC](http://www.ioc-sealevelmonitoring.org/list.php), [PSMSL](https://www.psmsl.org/data/obtaining/) maupun dari [Badan Informasi Geospasial](http://ina-sealevelmonitoring.big.go.id/ipasoet/data/map). 

Tapi terkadang data yang kita dapatkan masih dalam waktu UTC, sedangkan saat ingin memproses data tersebut, waktu yang digunakan adalah waktu lokal dari stasiun pengamatan, dan saya termasuk orang yang tidak rajin untuk mengubah waktunya satu persatu secara manual.

Nah, dalam tulisan ini kita akan mengubah waktu yang masih dalam UTC itu ke dalam waktu lokal, dengan masih menggunakan matlab. Script dan contoh datanya bisa didownload [di sini](https://1drv.ms/u/s!AlFYwWWftV2zmhZeyhN99fcxZ26L?e=ZC1b31).

**[Script Time Shifting](https://1drv.ms/u/s!AlFYwWWftV2zmhZeyhN99fcxZ26L?e=ZC1b31)**

Script yang saya bagikan juga ada dalam 2 versi, versi single file dan versi untuk multiple file. Step untuk running scriptnya juga masih sederhana dan input file nya dalam bentuk file excel. Langkah-langkahnya untuk versi multiple file adalah sebagai berikut :

- Seluruh data pengamatan dimasukkan ke dalam satu folder, contohnya folder dengan nama **utc**. Pastikan urutan data dalam file adalah tahun, bulan, hari, jam, menit, detik, dan data pengamatan pasutnya. Kalian bisa lihat dari contoh data yang kalian download.
- Tentukan zona waktu di lokasi pengamatannya, contoh dalam hal ini adalah dalam zona waktu GMT+7 atau Waktu Indonesia Barat.
- Buka script **multitimeshifting.m** dan pada baris ke-28 cari script seperti dibawah ini. Kalian perlu mengubahnya sesuai keperluan kalian.

```matlab
%shift zona waktu
utctime{i}=datetime(data{i}(:,1),data{i}(:,2),data{i}(:,3),data{i}(:,4),data{i}(:,5),data{i}(:,6),'Timezone','Asia/Jakarta'); 
```

- Untuk **WIB** formatnya adalah Asia/Jakarta GMT+7, untuk **WITA** adalah Asia/Makassar GMT+8, dan untuk **WIT** adalah Asia/Jayapura GMT+9.
- Setelah itu running scriptnya, dan akan keluar jendela dialog untuk memilih folder yang berisi data pengamatan dalam waktu utc.
- Jika sudah selesai, data pengamatan dengan waktu lokal akan tersimpan dalam folder **lokal**. 

![lokal](zone2.png)

Contoh data yang sudah diubah kedalam local time

Oke sip, Selamat mencoba.

