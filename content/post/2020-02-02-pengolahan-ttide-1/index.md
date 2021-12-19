---
title: Pengolahan data pasang surut menggunakan t_tide
date: 2020-02-02
slug: pengolahan-pasut-ttide-1
tags: 
- T-Tide
- Matlab
- Pasut
categories : 
- Hidrografi
typora-root-url: ./
---

Dalam ilmu geodesi, pasang surut air laut memiliki peranan penting dalam penentuan datum vertikal atau referensi acuan sebagai nilai elevasi suatu titik. Nilai **datum vertikal atau chart datum** ini diperoleh dari penjumlahan amplitudo nilai konstanta harmonik yang merupakan hasil pengolahan data pasut dari hasil pengamatan selama periode tertentu.

Nah salah satu tools yang bisa digunakan untuk mendapatkan nilai konstanta harmonik ini adalah T_Tide. **T_Tide** sendiri sebenarnya adalah kumpulan script yang berjalan pada software **MATLAB** yang dikembangkan oleh **Rich Pawlowicz**.

Contoh data dan T_Tide bisa di download disini :

1. [T_Tide](http://www.eos.ubc.ca/%7Erich/t_tide/t_tide_v1.4beta.zip)
2. [Contoh Data](https://1drv.ms/u/s!AlFYwWWftV2znn-Lp2FgpGUmT2Jt?e=DizYQF)

## **A. Analisa Konstanta Harmonik**

Pada dasarnya urutan dalam pengolahan menggunakan T_Tide adalah sebagai berikut :

1. Menyusun data lapangan ke dalam format yang didukung T_Tide, dengan menggunakan Ms. Excel, lalu simpan dalam file dengan ekstensi .txt atau xls. 
2. Menyusun script yang berisi perintah untuk memanggil data dan menjalankan program, kemudian simpan dalam format .m 
3. Panggil nama script file yang disimpan dan hasilnya bisa dilihat pada kolom display matlab,,

Data yang digunakan pada tutorial ini adalah data dari **stasiun pasut Pelabuhan Teluk Bayur Padang – Sumbar tahun 2015**, dengan periode pengamatan selama **1 tahun**, interval pengamatan **1 jam** dan nilai pengamatan pasut dalam satuan **meter**.
### 1. Data Input
Data input yang dibutuhkan pada tutorial ini berisi 7 kolom data yaitu tahun, bulan, hari, jam, menit, detik, dan tinggi pasut. Contoh data yang disertakan dalam 2 format yaitu .txt dan .xls.

![Contoh Data](ttide1.png)

Selain itu kita juga memerlukan data tambahan lain yaitu **Latitude atau lintang lokasi** stasiun pengamatan, pada contoh ini adalah **0° 59′ 48″ S**

Pastikan jumlah datanya sesuai, contoh jumlah data dalam rentang waktu 31 hari adalah 31 x 24jam = 744 data, jadi kalau kurang atau lebih dari itu maka perlu di cek lagi, kalau ada data pada waktu tertentu kosong maka perlu diisi dengan nilai **Nan.**

### 2. Menyusun Script
#### *2.1. Memanggil data input*
Dalam memanggil data, setiap format data memiliki perintah yang berbeda. Untuk format text cukup menggunakan perintah `load` dan diikuti dengan nama file yang ingin dipanggil. Sedangkan untuk format excel menggunakan perintah `xlsread` diikuti nama sheet yang berisi data.

```matlab
%contoh input format text
data=load('padang2015.txt');

%contoh input format excel
data=xlsread('padang2015.xlsx','Sheet1');
```
#### *2.2. Mendefinisikan kolom data*
Script ini berfungsi untuk mendefiniskan jenis data tiap kolom, hal ini dimaksudkan untuk mempermudah dalam proses pengolahan

```matlab
%contoh definisi kolom data
y=data(:,1); %tahun
m=data(:,2); %bulan
d=data(:,3); %hari
h=data(:,4); %jam
mn=data(:,5); %menit
sc=data(:,6); %detik
pasut=data(:,7); %tinggi pasut
```

#### *2.3. Menyusun data waktu dan lintang lokasi*
Selanjutnya kita juga perlu menyusun data waktu dan lintang lokasi yang berfungsi untuk koreksi nodal dari proses pengolahan pasang surut di T_Tide.

Penyusunan waktu di matlab menggunakan perintah `datenum`, dan untuk lintang lokasi menggunakan satuan derajat desimal dan dengan nilai positif ke arah utara dan negatif ke selatan.

```matlab
%menyusun data waktu dan lintang lokasi
time=datenum(y,m,d,h,mn,sc);
lat=(0+59/60+48/3600)\*-1;
```

#### *2.4. Script fungsi analisa konstanta harmonik*
Secara singkat, perintah untuk melakukan analisa konstanta harmonik adalah dengan menggunakan perintah ` [TIDESTRUC,XOUT]=t_tide(data input)`

Tapi kita memiliki beberapa opsi input untuk mendapatkan hasil pengolahan yang akurat, sehingga script akhirnya adalah sebagai berikut

```matlab
[TIDESTRUC,XOUT]=t_tide(pasut,'interval',1,'start',time(1),...
'latitude',lat,'output','hasil_analisa.txt');
```

Opsi :  
`'interval'` = untuk mendefinisikan interval data yang digunakan. Unit satuan dalam jam. Misal interval 15menit menjadi 1/4, 30 menit jadi 1/2. Default = 1  
`'start'` = waktu mulai pengamatan.  
`'output'` = opsi untuk menyimpan hasil pengolahan.  

Setelah itu simpan script file nya, misal dengan nama file padang1, nantinya file tersebut akan tersimpan dengan ekstensi **.m**.

### 3. Menjalankan Script Analisa Konstanta Harmonik
Sebelum menjalankan script, untuk memudahkan, pastikan file script dan raw data berada di dalam folder T_Tide, atau bisa juga dalam matlab dengan mengkatifkan path folder dan subfolder dari folder penyimpanan T-Tide.

Setelah itu pada matlab, ketik di dalam command window nama script file yang sudah kita simpan tadi, lalu klik enter.

![Running Script](ttide2.png)

![Process Run](ttide3.png)

Hasil konstanta harmonik pasut nantinya akan tersimpan dengan nama seperti yang didefinisikan pada opsi `'output'`. 

Contoh isi dari output hasil pengolahan di atas adalah sebagai berikut,,

![Contoh Hasil](ttide4.png)

Dari output diatas bisa dilihat beberapa informasi penting yang terkandung, diantaranya :

1. Jumlah data yang dipakai dan panjang waktu pengamatan
2. Waktu awal pengamatan
3. **x0** adalah nilai **Mean Sea Level** dengan satuan yang sama seperti data inputnya
4. Konstanta Harmonik yang dihasilkan beserta nilai amplitudo (amp), nilai phase(pha) dan SNR (Signal to noise rasio). Konstanta harmonik dianggap signifikan bilai nilai **SNR > 2 (default)**, dan ditandai dengan tanda bintang (\*) diawal nama konstanta harmonik.

---

## **B. Prediksi Pasang Surut Laut**
Prediksi pasut pada T_Tide didapat dengan memanfaatkan hasil analisa harmonik yang diperoleh dari proses di atas.

Prediksi pasut ini memiliki beberapa kegunaan, diantaranya adalah :

1. Untuk mengetahui jadwal dan ketinggian permukaan air laut dalam kaitannya untuk perencanaan operasi pemetaan maupun inspeksi infrastruktur bawah laut baik diwilayah pesisir maupun lepas pantai.
2. Sebagai koreksi kedalaman terhadap referensi chart datum yang digunakan selama operasi
3. Merencanakan jadwal keluar masuk kapal dalam area pelabuhan
4. Untuk pengembangan metode numerik untuk memprediksi dampak evolusi permukaan laut terhadap wilayah pesisir
5. Dan lain lain,,,

### 1. Menyusun Durasi dan Interval Prediksi
Untuk menjalankan script prediksi pasut, pertama perlu ditentukan lama atau durasi dari prediksi yang diinginkan, input yang diperlukan adalah **waktu awal dan akhir prediksi**. misal kita ingin mendapatkan prediksi pasut tahun 2018 selama setahun dengan interval tiap 1 jam, contoh scriptnya adalah sebagai berikut

```matlab
%waktu awal dan akhir untuk prediksi
awal=datenum(2018,1,1,0,0,1);
akhir=datenum(2018,12,31,23,59,59);

%durasi prediksi
durasi=awal:1/24:akhir;
```

Angka pembagi 1/24 di atas berarti interval tiap 1 jam, Jika ingin interval tiap 30 menit maka pembaginya adalah 1/24/2, untuk 15 menit adalah 1/24/4, dst.

Jika kita menginginkan interval data seperti input dari data analisa sebelumnya, maka sebelum script durasi prediksi, kita perlu definiskan kembali dengan script berikut

```matlab
%mencari interval data sama dengan input data
interval=(time(2)-time(1));

%durasi prediksi sama dengan data input
durasi=awal:interval:akhir;
```

### 2. Menjalankan Script Prediksi
Dalam pengolahannya di T_Tide, fungsi yang digunakan untuk melakukan prediksi adalah `t_predic` dengan format sebagai berikut :

`YOUT=t_predic(TIM,TIDESTRUC,...)`

`TIM` = waktu prediksi yang diinginkan dalam *decimal days* yang berasal dari Datenum.

`TIDESTRUC` = output dari analisa harmonik t_tide.

Apabila sudah menentukan waktu awal, akhir dan interval prediksi, maka scriptnya akan menjadi

`YOUT=t_predic(durasi,TIDESTRUC);`

Hasil prediksi ini masih berupa matriks variable di dalam matlab, untuk menyimpan hasil prediksinya kita bisa menggunakan perintah yang disediakan oleh matlab seperti dibawah ini, misal kita ingin menyimpan hasil predikski dalam format text dengan nama **prediksi2018.txt**

```matlab
fid=fopen('prediksi20181.txt','w');ct=[cellstr(datestr(durasi,'yyyy-mm-dd HH:MM')) num2cell(transpose(YOUT))].'; 
fprintf(fid,'%s \n','Tanggal Tinggi'); 
fprintf(fid,'%s %6.3f \n',ct{:}); 
fclose(fid);
```

Terakhir simpan script di atas, misal dengan nama **padang2.m**, lalu panggil script tsb di dalam command window matlab, jika berhasil maka hasil prediksinya akan muncul seperti berikut

 ![Contoh Prediksi](ttide5.png)

Oke secara umum fungsi analisa dan prediksi pasut menggunakan T_TIDE adalah seperti yg sudah dijelaskan di atas, untuk membuat grafik dan variasi lain dalam menggunakan T_Tide mungkin akan bersambung dalam postingan berikutnya. 

Terimakasih sudah mampir, dan semoga bermanfaat :D

Sumber:  
**R. Pawlowicz, B. Beardsley, and S. Lentz, “Classical tidal harmonic analysis including error estimates in MATLAB using T_TIDE”, Computers and Geosciences 28 (2002), 929-937**