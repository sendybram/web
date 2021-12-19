---
title: Installasi MB-System dalam Ubuntu 20.04
date: 2020-04-05
slug: installasi-mbsystem-dalam-ubuntu2004
toc: true
tags: 
- Multibeam
- MBES
- MB-System
- Linux
categories : 
- Hidrografi
typora-root-url: ./
---

**0.1 UPDATE 12 Mei 2020**  
**0.2 UPDATE 28 Desember 2020**

MB-System merupakan software yang bersifat open source yang diperuntukkan untuk pengolahan dan visualisasi data batimetri dan image backscatter dari multibeam, interferometry dan sidescan sonar. MB-System dikembangkan oleh *Lamont-Doherty Earth Observatory of Columbia University (L-DEO)*, dan berkolaborasi dengan *Monterey Bay Aquarium Research Institute (MBARI) University of New Hampshir*e dan MARUM. *(source : [**MBARI**](https://www.mbari.org/products/research-software/mb-system/))*

Disini saya akan membahas langkah-langkah installasi MB-System ini dalam sistem operasi Ubuntu 20.04, tapi masih relevan bila ingin diinstall dalam sistem operasi lain selama masih dalam turunan distro Debian atau distro Ubuntu, seperti linux mint, kubuntu dll. 

## **A. Instalasi Default**

MB-System secara default akan ter install dalam folder ***/usr/local*** di dalam sistem root, selain itu MB-System juga memerlukan software OTPS sebagai additional software untuk koreksi pasut data multibeam ke dalam mean sea level. OTPS menggunakan model pasut global dari OSU TPXO Tide Models.

Langkah installasinya adalah sebagai berikut :

### **A.1. Persiapan**

1. Setelah install Ubuntu 20.04 buka terminal dan install beberapa software pendukung dengan run 

```bash
sudo apt-get install libx11-dev xorg-dev libmotif-dev mesa-common-dev libsdl1.2-dev libsdl-image1.2-dev build-essential gfortran libfftw3-3 libfftw3-dev libnetcdf-dev netcdf-bin gdal-bin libgdal-dev gv csh libgmt-dev libproj-dev gmt gmt-dcw gmt-gshhg autoconf git
```

2. Download OTPS software di web resmi [OTPS](https://drive.google.com/file/d/1FBlS_Xmf6_dnCg1T0t5GSTRTwMjLuA8N/view?usp=sharing) dan MB-System terbaru di [MB-System](https://github.com/dwcaress/MB-System/archive/5.7.5.tar.gz). Secara default hasil download itu nanti akan tersimpan di folder Download di dalam /home. Unzip keduanya, apabila nama folder hasil ekstraksinya terlalu panjang rename menjadi **mbsystem** agar lebih simple dan untuk otps rename menjadi **OTPS2** agar bisa langsung diidentifikasi oleh MB-System.

[Official OTPS](https://drive.google.com/file/d/1FBlS_Xmf6_dnCg1T0t5GSTRTwMjLuA8N/view?usp=sharing)

[OTPS Alt](https://archive.org/details/otps2)

[MB-System](https://github.com/dwcaress/MB-System/archive/5.7.6.tar.gz)

- ***Alternatif Download MB-System Via Terminal***
  Pada terminal, ketik kan perintah berikut

  `git clone https://github.com/dwcaress/MB-System.git`

Setelah proses selesai, maka akan ada folder baru bernama MB-System. Cara ini berguna untuk mendapatkan MB-System versi terbaru, walaupun versi terbaru merupakan versi beta, tapi mendapatkan perbaikan bug dari versi sebelumnya.

3. Setelah itu kita perlu mendownload data model pasut global, dalam tulisan ini saya memakai model **tpxo8\_atlas\_30\_v1**. Dalam model pasut global yang telah didownload akan ada 3 file yaitu ***uv.tpxo8\_atlas\_30\_v1, hf.tpxo8\_atlas\_30\_v1*** dan ***grid\_tpxo8atlas\_30\_v1***. Selanjutnya ketiga file itu kita masukkan kedalam folder **DATA** yang ada di dalam folder **OTPS2**.

*note : Software OTPS memerlukan data TPXO atlas tide model, tapi sejak 2018 OSU Tide Group tidak lagi mengijinkan untuk mendownload data ini secara bebas. Bila ingin mendapatkan data ini untuk penelitian, prosedurnya bisa dilihat dalam web resmi [**OSU TPXO Tide**](https://www.tpxo.net/tpxo-products-and-registration) untuk mendapatkan akses downloadnya.*

4. Masuk kedalam folder **OTPS2/DATA** dan edit file Model\_atlas, kemudian isikan nama ketiga file tadi lengkap dengan path locationnya seperti dibawah ini :
*/usr/local/OTPS2/DATA/hf.tpxo8\_atlas\_30\_v1  
/usr/local/OTPS2/DATA/uv.tpxo8\_atlas\_30\_v1  
/usr/local/OTPS2/DATA/grid\_tpxo8atlas\_30\_v1*

5. Kembali lagi ke dalam folder **OTPS2,** buka file *setup.inp*, dan pastikan pada baris pertama adalah
*DATA/Model\_atlas ! 1. tidal model control file*

(nama file Model\_atlas bisa bervariasi pada setiap update, jadi sesuaikan dengan nama file Model\_atlas seperti yang ada dalam folder DATA)

### **A.2. Instalasi**

6. Setelah semua persiapan selesai, pindahkan folder OTPS2 dan mbsystem ke dalam folder */usr/local lewat terminal.*

	- buka terminal, masuk ke dalam lokasi penyimpanan, misal folder Download dengan perintah cd Downloads
	- masuk dalam mode **super user** dengan perintah sudo su dan masukkan password kita
	- copy folder OTPS2 dan mbsystem ke dalam folder */usr/local* dengan perintah 
`cp -r OTPS2 mbsystem /usr/local`
	- masuk ke folder otps cd */usr/local/OTPS2*

7. Setelah masuk kedalam folder OTPS2, saatnya kita compile software OTPS.
	- ketik `make extract\_HC`, tunggu sampai selesai.
	- ketik `make predict\_tide`, tunggu sampai selesai. 

8. Selanjutnya masuk ke folder mbsystem 
	- dari terminal, kembali ke folder local cd ..
	- masuk ke folder mbsystem cd mbystem
	- compile dengan perintah ./configure
	- setelah selesai, dilanjutkan make
	- terakhir make install

9. Setelah proses installasi selesai, close terminal lalu buka terminal baru. 
	- Pada terminal baru masukkan perintah 
	`export LD\_LIBRARY\_PATH=/usr/local/lib:$LD\_LIBRARY\_PATH` 
	- Setelah itu masukkan sudo ldconfig
	- Ketik password lalu enter

10. Setelah semua selesai, coba kita run perintah MB-System untuk mengecek apakah software ini sudah terinstal dengan benar, bila software telah terinstal dengan benar hasilnya seperti contoh berikut.

![](mbs_1.png)

11. Untuk model pasut global dari OTPS yang sudah terintegrasi dengan MB-System, hasilnya adalah seperti dibawah ini

![](mbs_2.png)

-----

## **B. Installasi Dalam Home Folder**

Metode installasi ini sama seperti sebelumnya, hanya yang membedakan adalah MB-System akan diinstall dalam **Home Folder**. Metode ini bisa menjadi alternatif bagi pengguna yang memiliki kriteria :

- Volume partisi Home Folder yang lebih besar dari partisi Root
- Volume partisi Root sudah mulai habis
- Tidak ingin mengotak-atik system Root dalam linux
- Ingin mencoba cara installasi yang lebih mudah :)

Artikel ini mengambil referensi dari salah satu tulisan developer MB-System tentang installasi MB-System dalam sandbox, yang pernah diunggah dalam grup milis yang bisa dibaca [disini](https://listserver.mbari.org/sympa/arc/mbsystem/2017-01/msg00035.html).

### **B.1. Proses Installasi**

1. Untuk persiapan langkahnya sama seperti point **A.1.1** dan **A.1.2**
2. Buat folder baru di dalam home folder dengan nama **sandbox**. Copy folder OTPS2 dalam folder download ke dalam folder sandbox.
3. Masuk ke dalam folder **/sandbox/OTPS2/Data** kemudian edit file Model\_atlas\_v1, isikan nama ketiga file yang ada dalam folder **Data** lengkap dengan path locationnya seperti contoh dibawah ini :

*/home/sendybram/sandbox/OTPS2/DATA/hf.tpxo8\_atlas\_30\_v1  
/home/sendybram/sandbox/OTPS2/DATA/uv.tpxo8\_atlas\_30\_v1  
/home/sendybram/sandbox/OTPS2/DATA/grid\_tpxo8atlas\_30\_v1*

**Note** : ganti nama *sendybram* dalam */home/sendybram/* dengan nama username desktop linux kalian

4. Kembali ke folder **OTPS2**, buka file *setup.inp* dan pastikan pada baris pertama adala
*DATA/Model\_atlas\_v1 ! 1. tidal model control file*

5. Buka folder OTPS2 melalui terminal lalu compile OTPS2 dengan run 2 perintah
	– ketik `make extract\_HC`, tunggu sampai selesai.
	– ketik `make predict\_tide`, tunggu sampai selesai.
	– Kita bisa coba hasil compile kita dengan run perintah `./extract\_HC<setup.inp`

Dari perintah di atas akan mengeluarkan output bernama **sample.out** dalam folder OTPS2.

6. Pada terminal yang sama, kembali ke folder sandbox dari folder OTPS2 (cd..) lalu run
`git clone https://github.com/dwcaress/MB-System.git`
setelah proses selesai, maka dalam folder sandbox akan ada folder baru bernama MB-System
7. Masuk ke folder MB-System (cd MB-System), 
- compile dengan perintah

`./configure --prefix=/home/sendybram/sandbox/MB-System --with-otps-dir=/home/sendybram/sandbox/OTPS2`

- setelah selesai, dilanjutkan `make`
- terakhir `make install`

8. Setelah selesai, masih di terminal, buka file *.profile* dengan run perintah `gedit ~/.profile`

lalu copy line dibawah ini ke dalam file *.profile*

`export PATH="$PATH:/home/sendybram/sandbox/OTPS2:/home/sendybram/sandbox/MB-System/bin"`

9. Log out dari desktop linux lalu log in kembali.
10. Buka terminal, lalu coba masukkan perintah dari MB-System untuk mengetes fungsinya.

![](mbs_3.png)

Contoh hasil perintah MB-System, mbinfo dan mbotps 

## **C. Ekstra Steps**

Extra steps disini merupakan langkah tambahan untuk dapat menjalankan seluruh fungsi dan fitur-fitur yang ada dalam MB-System.

1. Multi CPU Macros
	- Untuk menggunakan fungsi macro, kita perlu menginstall modul Perl atau Parallerl-ForkManager. 
	- Download di [perl](https://metacpan.org/pod/Parallel::ForkManager), cari kolom Tools di bar sebelah kiri, dibawahnya akan ada opsi Download. Setelah didownload, unzip file tersebut.
	- masuk ke dalam folder lewat terminal kemudian masukkan commands :
```bash
sudo perl Makefile.PL
sudo make install
```

2. Menggunakan perintah MB-System yang terintegrasi dengan commands GMT
	- buka terminal dan masukkan perintah berikut
	`gmt set GMT\_CUSTOM\_LIBS /usr/local/lib/mbsystem.so`
	contoh perintah MB-System yang sudah terintegrasi dengan GMT adalah seperti dibawah ini.

3. menonaktifkan kompresi pada grid netcdf di MB-System
	- Buka terminal dan masukkan perintah berikut
	`gmt set IO\_NC4\_CHUNK\_SIZE classic`

![](mbs_4.png)

Contoh perintah MB-System yang sudah terintegrasi dengan GMT

## **D. SAMPLE and TUTORIAL**

Tutorial dan contoh data ini dibagikan oleh salah satu developer MB-System, Christian Ferreira, semuanya bisa di download di [sini](https://drive.google.com/drive/folders/0B68D9FxpOcmPfmlOMjljSWlQcVR5bmJySkdJZ09YdHJ3VkhUcFJCajlXRjBVR1NOdlE1UVE).

Referensi :
https://www.mbari.org/products/research-software/mb-system/

