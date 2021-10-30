# Jarkom-Modul-2-B12-2021

Anggota: Faisal Reza M 05111940000009 (screenshot, cara pengerjaan, kendala)

### 1. EniesLobby akan dijadikan sebagai DNS Master, Water7 akan dijadikan DNS Slave, dan Skypie(a) akan digunakan sebagai Web Server. Terdapat 2 Client yaitu Loguetown, dan Alabasta. Semua node terhubung pada router Foosha, sehingga dapat mengakses internet
Saya mengikuti langkah-langkah yang sudah ada di modul, maka akan sudah ada 4 node, yaitu EniesLobby, Water7, Loguetown, Alabasta, lalu, saya hanya perlu menambahkan node ke-5, yaitu Skypie, menggunakan langkah yang sama seperti di modul, lalu mengatur ip-nya menjadi 10.12.2.4. Hasil akhir:
![image](https://user-images.githubusercontent.com/11045113/139533712-42e28eac-eb39-4a16-a085-a49badcdfe29.png)

### 2. Luffy ingin menghubungi Franky yang berada di EniesLobby dengan denden mushi. Kalian diminta Luffy untuk membuat website utama dengan mengakses franky.yyy.com dengan alias www.franky.yyy.com pada folder kaizoku
Kita akan membuat script.sh untuk otomasi setup server. Script ini nanti akan dipanggil oleh .bashrc saat startup node. Adapun yang dilakukan script ini adalah:
  - echo nameserver, agar bisa akses internet untuk download software2
  - apt update, lalu mendownload bind9
  - membuat folder kaizoku untuk config server utama
  - menjalankan copy.sh, yang akan memindahkan config web dari folder /root, script ini dijadikan sendiri agar mudah jika ingin mengubah2 config webserver

![image](https://user-images.githubusercontent.com/11045113/139534173-18434be4-cd4d-41cc-92fc-914cc9254b84.png)
 
 Lalu, kita akan membuat script copy.sh itu sendiri, dia akan:
  - menyalin file named.conf.local dan named.conf.options yang telah kita edit dari /root ke /etc/bind
  - menyalin franky.b12.com ke /etc/bind/kaizoku
  - merestart bind9

![image](https://user-images.githubusercontent.com/11045113/139534460-c6e89e89-88c4-4485-9ccc-b09dadfae1a5.png)

Isi file nc.local

![image](https://user-images.githubusercontent.com/11045113/139534471-6f52f7d2-1f83-49f5-b1a7-6358403e9ddc.png)

Untuk file nc.options, kita hanya perlu mengcomment line `dnssec-validation auto;`, dan menambahkan line `allow-query{ any; };` di bawahnya

Isi file franky.b12.com (digunakan CNAME untuk menambahkan alias www)

![image](https://user-images.githubusercontent.com/11045113/139534596-ca9f00e4-87c5-4f80-9664-8e80e6747f1e.png)

Hasil tes ping

![image](https://user-images.githubusercontent.com/11045113/139534648-716430a6-78ea-4196-8bf3-29cfe31c999b.png)

### 3. Setelah itu buat subdomain super.franky.yyy.com dengan alias www.super.franky.yyy.com yang diatur DNS nya di EniesLobby dan mengarah ke Skypie
Tambahkan line super dan satu line di bawahnya di franky.b12.com, lalu jalankan copy.sh

![image](https://user-images.githubusercontent.com/11045113/139536956-ed9de9bb-b6e8-42ee-96a5-4221d4e5f036.png)

Tes dengan mengeping mereka di Loguetown

![image](https://user-images.githubusercontent.com/11045113/139536970-0be5b2e1-870f-4079-8c85-b7b257292c82.png)

### 4. Buat juga reverse domain untuk domain utama
Buat file 2.12.10.in-addr.arpa, isi sbb:

![image](https://user-images.githubusercontent.com/11045113/139537149-4b4a78ca-c366-481b-8c54-2ef9c4856c38.png)

Tambahkan zone yang bawah ke nc.local

![image](https://user-images.githubusercontent.com/11045113/139537174-37b55023-a0d5-4fc4-9ed5-ac46a1e59150.png)

Tambahkan line di copy.sh untuk memindahkan file baru tsb ke kaizoku, lalu jalankan copy.sh. Tes di loguetown

![image](https://user-images.githubusercontent.com/11045113/139537319-eaef8d48-7e1b-4ea5-9e09-8a6edf302f3f.png)

### 5. Supaya tetap bisa menghubungi Franky jika server EniesLobby rusak, maka buat Water7 sebagai DNS Slave untuk domain utama
Pada Enieslobby, edit nc.local

![image](https://user-images.githubusercontent.com/11045113/139537617-19d197b0-6286-4260-a16d-d353f4868ec0.png)

lalu di water7 (di water7 juga ada script.sh dan copy.sh) edit nc.local

![image](https://user-images.githubusercontent.com/11045113/139537638-4fe2ad2e-34f4-446a-bd97-8f3eec81de70.png)

Jalankan copy.sh di kedua node. Matikan bind9 di EniesLobby dengan `service bind9 stop` lalu tes di Loguetown

![image](https://user-images.githubusercontent.com/11045113/139537491-28f8294c-1550-4ae8-8c2c-c680b591b077.png)
Masih bisa diakses

### 6. Setelah itu terdapat subdomain mecha.franky.yyy.com dengan alias www.mecha.franky.yyy.com yang didelegasikan dari EniesLobby ke Water7 dengan IP menuju ke Skypie dalam folder sunnygo
Di enies lobby, edit franky.b12.com (tambahkan ns1 dan mecha):

![image](https://user-images.githubusercontent.com/11045113/139537837-83e683cc-d2c5-4460-a2ff-cd118bc75595.png)

Di water7, edit nc.options. comment `dnssec-validation auto;` dan tambahkan line `allow-query{any;};`

edit nc.local

![image](https://user-images.githubusercontent.com/11045113/139537811-d7fabf7d-6910-4b2a-b7ed-f4892a7663ec.png)

edit mecha.franky.b12.com

![image](https://user-images.githubusercontent.com/11045113/139537971-4ecdf7bc-07fc-42c9-936e-7a04ef8d0131.png)

Jalankan copy.sh di kedua node. Tes di Loguetown

![image](https://user-images.githubusercontent.com/11045113/139537385-946955e2-508d-4336-b2c3-1fbf5a0ba838.png)

### 7. Untuk memperlancar komunikasi Luffy dan rekannya, dibuatkan subdomain melalui Water7 dengan nama general.mecha.franky.yyy.com dengan alias www.general.mecha.franky.yyy.com yang mengarah ke Skypie
DI water7, tambahkan line general dan www.general di mecha.franky.b12.com

![image](https://user-images.githubusercontent.com/11045113/139538051-979ad4b8-7b29-4158-a05b-86ece3500bf0.png)

Jalankan copy.sh. Tes di Loguetown

![image](https://user-images.githubusercontent.com/11045113/139537443-dc55e62e-d72d-4056-9412-cc4fea0fca14.png)

### 8. Setelah melakukan konfigurasi server, maka dilakukan konfigurasi Webserver. Pertama dengan webserver www.franky.yyy.com. Pertama, luffy membutuhkan webserver dengan DocumentRoot pada /var/www/franky.yyy.com.
Di server skypie, kita juga sama, menggunakan script.sh, tapi perbedaannya, script ini (jika ada command di screenshot tapi tidak saya list, berarti itu command yang tidak berguna tapi lupa saya hapus):
  - echo nameserver
  - apt update, lalu mendownload apache2, php, dan libapache
  - membuat folder franky.b12.com
  - mengstart apache
  - memanggil copy.sh

![image](https://user-images.githubusercontent.com/11045113/139535201-836a4a82-0ef6-417f-b8b7-60a4a5e64e60.png)

Lalu, script copy.sh akan melakukan:
  - menyalin index.php dan home.html yang telah didownload dari github ke /var/www/franky.b12.com
  - menyalin franky.b12.com.conf ke sites-available
  - jalankan a2ensite franky.b12.com.conf
  - service apache2 restart

![image](https://user-images.githubusercontent.com/11045113/139536839-d459fabf-60bb-46eb-bef4-bf22805c1c7d.png)

Adapun isi franky.b12.com.conf

![image](https://user-images.githubusercontent.com/11045113/139535991-6f827cfb-b86f-4642-a921-745f56cb2bce.png)

Hasil setelah kita membuka `lynx www.franky.b12.com` di Loguetown

![image](https://user-images.githubusercontent.com/11045113/139536020-9bc4c8aa-d15f-4081-b6aa-44c3fbb376c0.png)

### 9. Setelah itu, Luffy juga membutuhkan agar url www.franky.yyy.com/index.php/home dapat menjadi menjadi www.franky.yyy.com/home.
Kita harus menambahkan file .htaccess, dengan isi sbb:

![image](https://user-images.githubusercontent.com/11045113/139536466-d26bf0d3-b06c-487d-acd0-a5d0f98799ea.png)

Lalu, menambahkan line berikut di franky.b12.com.conf

![image](https://user-images.githubusercontent.com/11045113/139536516-f2e6cd06-1f4d-4ddf-bfc2-242428b70ba5.png)

Lalu kita hanya perlu menambahkan line di script untuk memindahkan htaccess ke var/www/franky.b12.com. Lalu kita tes dengan menjalankan di loguetown `lynx www.franky.b12.com/home`. Hasil:

![image](https://user-images.githubusercontent.com/11045113/139536563-348d11a5-ca3b-41c6-a904-87a93f4eacbc.png)

### Untuk nomor 10-17, mohon maaf, saya belum selesai, karena kendala yang saya paparkan di bawah

### Kendala
 - Tidak ada teman untuk diskusi (karena praktikum saya sendirian), sehingga saya kadang telat menyadari kesalahan fatal, misal saya membuat webserver di water7 (mengikuti modul), padahal harusnya di skypie(a)
 - Lalu, saat saya memindahkan script ke dari Water7 ke Skypie, masih ada command2 webserver (misal copy named.conf.local) yang terikut sehingga membuat script berantakan dan membingungkan
 - Jadwal saya cukup padat pada hari senin selasa (ada 5 matkul, dan salah satu matkul tsb adalah MPPL dengan dosen Bu Sarwosri) sehingga saya tidak bisa mengerjakan dengan maksimal
 - Saya tidak tahu jika revisinya langsung setelah demo (hanya ada waktu 3 jam), jadi saya sengaja tidak revisi setelah mengumpulkan (takutnya GNS malah rusak sebelum demo)
