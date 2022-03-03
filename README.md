# Soal 3

Buatlah program monitoring resource pada komputer kalian. Cukup monitoring ram dan monitoring size suatu directory. Untuk ram gunakan command `free -m`. Untuk disk gunakan command `du -sh <target_path>`. Catat semua metrics yang didapatkan dari hasil `free -m`. Untuk hasil `du -sh <target_path>` catat size dari path directory tersebut. Untuk target_path yang akan dimonitor adalah /home/{user}/.

A. Masukkan semua metrics ke dalam suatu file log bernama metrics_{YmdHms}.log. {YmdHms} adalah waktu disaat file script bash kalian dijalankan.

B. Script untuk mencatat metrics diatas diharapkan dapat berjalan otomatis pada setiap menit.

C. Kemudian, buat satu script untuk membuat agregasi file log ke satuan jam. Script agregasi akan memiliki info dari file-file yang tergenerate tiap menit. Dalam hasil file agregasi tersebut, terdapat nilai minimum, maximum, dan rata-rata dari tiap-tiap metrics. File agregasi akan ditrigger untuk dijalankan setiap jam secara otomatis. Berikut contoh nama file hasil agregasi metrics_agg_2022013115.log dengan format metrics_agg_{YmdH}.log

D. Karena file log bersifat sensitif pastikan semua file log hanya dapat dibaca oleh user pemilik file.

## Penjelasan Code Soal 3
Dalam soal ini kita diminta untuk membuat sebuah script bernama **minute_log.sh** untuk mencatat metrics tiap menit dan buat outputnya dengan format **metrics_{YmdHms}.log** di directory log. Setelah itu, buat script bernama **aggregate_minutes_to_hourly_log.sh** untuk mengagregasi file log ke satuan jam dengan output berformat **metrics_agg_{YmdH}.log**.

## A
Kita diminta untuk membuat sebuah script bernama **minute_log.sh** dan output bernama **metrics_{YmdHms}.log** berisi data metrics yang diambil dari 'free -m' untuk menghitung memory dan 'du -sh /home/{user}/' untuk menghitung size directory.

- Data hasil dari 'free -m' dan 'du -sh'
![1]()

### Mengambil data memory dan size directory
```bash
memory=$(free -m | awk '/^Mem/ {print $2","$3","$4","$5","$6","$7}')
swap=$(free -m | awk '/^Swap/ {print $2","$3","$4}')
```
Kode diatas digunakan untuk mengambil data dari 'free -m' yang berisi data dari memory komputer. Awk menggunakan pipe untuk mengambil data dari 'free -m', dan '^Mem' akan membaca baris yang memiliki kata-kata "Mem", begitu juga dengan  "^Swap". Tanda Dollar digunakan untuk menentukan data mana yang akan di ambil berdasarkan kolom. Misalkan kita mengetik  "$2", maka script akan mengambil data dari kolom 2. Data tersebut kemudian di masukkan ke dalam variabel yang bernama "memory" dan "swap".

```bash
path=$(du -sh /home/satrio/ | awk '{print $2","$1}')
namafile="metrics_$(date +"%Y%m%d%I%M%S").log"
```
Kode diatas berguna untuk mengambil ukuran dari directory yang ditentukan. Awk akan menggunakan pipe untuk mengambil data dari 'du -sh' dan print kolom ke 2 dan ke 1. Kemudian data tersebut akan dimasukkan ke variabel "path". Selanjutnya adalah menentukan tujuan dari output script ini. Seperti format yang diminta di soal, file output dari script ini bernama "**metrics_{YmdHms}.log**" yang berisi Year(%Y), Month(%m), Date(%d), Hour(%I), Minute(M), dan Seconds(%S). Disini menggunakan Date Command untuk menanmpilkan format waktu. Selanjutya adalah memasukkan format file terebut ke dalam variabel yang bernama "namafile".

### Mengatur output
```bash
echo "$memory,$swap,$path" >> /home/satrio/log/$namafile
```
Disini script ini akan mengeprint atau mengecho variabel "memory", "swap", dan "path", kemudian akan disimpan di directory /home/{user}/log/ dengan format nama sesuai dengan variabel "namafile".

## B
Disoal ini, kita diminta untuk dapat menjalankan scriptnya secara otomatis setiap menit. Disini kita akan menggunakan cronjob untuk membuat script ini berjalan setiap menit.
```
* * * * * /home/satrio/soal-shift-sisop-modul-1-itb02-2022/soal3/minute_log.sh >> /home/satrio/log/metrics.log 2>&1
``` 

![1](https://raw.githubusercontent.com/Satriokml/gambar/main/Screenshot%202022-03-02%20171017.png)
