---
title: "Smartfren Haier CE81B on Ubuntu 12.04"
date: "2014-03-13"
categories: 
  - "code"
  - "lesson-learned"
  - "review"
  - "technology"
  - "tips"
tags: 
  - "ce81d"
  - "haier"
  - "smartfren"
  - "ubuntu"
---

Setelah menghabiskan waktu berjam-jam, akhirnya bisa juga menggunakan modem yang satu ini di Ubuntu 12.04.

Dari sekian banyak hasil pencarian Google, [halaman yang satu ini yang akhirnya membuahkan hasil](http://emensite.blogspot.com/2012/04/cara-install-modem-smart-di-ubuntu-1110.html). Thanks!

_Berikut adalah beberapa catatan versi gw sendiri._

Modem ini punya dua mode:

1. mode penyimpanan (storage mode) yang biasanya akan aktif di Windows/OSX. apabila diakses dengan file manager, kita bisa mendapatkan installer software yang menangani modem
2. modem (modem mode) yang akan aktif setelah mode penyimpanan dideaktif

Pada lingkungan Windows/OSX, aplikasi modem yang kita install akan menangani deaktivasi mode penyimpanan, lalu mengaktivasi modem. Namun seperti yang kita ketahui, aplikasi modem tersebut tidak tersedia untuk Ubuntu (ataupun distribusi GNU/Linux lainnya).

Intinya, agar dapat menggunakan modem CE81B ini, kita harus 1) deaktivasi mode penyimpanan, 2) mengaktifkan mode modem, dan 3) menjalankan modem.

Untuk 1 dan 2, kita dapat menggunakan perintah:

sudo modprobe usbserial vendor=0x201e product=0x10f8

Modprobe digunakan untuk menambahkan/mengeluarkan modul dari kernel Linux. Dalam hal ini kita memberi tahu kernel agar menggunakan modul usbserial  ketika mendeteksi modem CE81B. Tanpa perintah ini, CE81B akan menggunakan modul usb-storage  (penyimpanan dengan USB).

Perhatian: perintah modprobe di atas dijalankan _\*sebelum\*_ memasang CE81B di komputer. Jika CE81B sudah terpasang maka kernel akan menggunakan modul usb-storage dan perintah modprobe di atas jadi tidak berguna. Oleh karena itu, silahkan cabut CE81B, restart komputer, jalankan perintah modprobe di atas, lalu pasang CE81B ke komputer.

Perhatikan nilai 0x201e  pada atribut vendor, dan nilai 0x10f8  pada atribut product. Nilai tersebut adalah identitas milik CE81B yang didapat dari perintah:

➜ ~ lsusb
Bus 002 Device 014: ID 201e:10f8 
➜ ~

Gunakan perintah di bawah untuk memastikan bahwa kernel berhasil deaktivasi mode penyimpanan dan aktivasi mode modem:

cat /var/log/syslog

Cari output yang kira-kira seperti ini:

Mar 14 00:53:03 endeavor kernel: \[  756.504372\] usb 2-1.2: new high-speed USB device number 13 using ehci\_hcd
Mar 14 00:53:03 endeavor mtp-probe: checking bus 2, device 13: "/sys/devices/pci0000:00/0000:00:1d.0/usb2/2-1/2-1.2"
Mar 14 00:53:03 endeavor mtp-probe: bus: 2, device: 13 was not an MTP device
Mar 14 00:53:03 endeavor kernel: \[  756.601156\] scsi10 : usb-storage 2-1.2:1.0
Mar 14 00:53:04 endeavor kernel: \[  757.602271\] scsi 10:0:0:0: CD-ROM            Haier    CD-ROM           2.31 PQ: 0 ANSI: 2
Mar 14 00:53:04 endeavor kernel: \[  757.605358\] sr1: scsi-1 drive
Mar 14 00:53:04 endeavor kernel: \[  757.605559\] sr 10:0:0:0: Attached scsi CD-ROM sr1
Mar 14 00:53:04 endeavor kernel: \[  757.605682\] sr 10:0:0:0: Attached scsi generic sg2 type 5
Mar 14 00:53:04 endeavor usb\_modeswitch: switching device 05c6:f000 on 002/013
Mar 14 00:53:04 endeavor kernel: \[  758.096598\] usb 2-1.2: USB disconnect, device number 13
Mar 14 00:53:08 endeavor kernel: \[  761.877580\] usb 2-1.2: new high-speed USB device number 14 using ehci\_hcd
Mar 14 00:53:08 endeavor mtp-probe: checking bus 2, device 14: "/sys/devices/pci0000:00/0000:00:1d.0/usb2/2-1/2-1.2"
Mar 14 00:53:08 endeavor mtp-probe: bus: 2, device: 14 was not an MTP device
Mar 14 00:53:08 endeavor kernel: \[  761.973994\] usbserial\_generic 2-1.2:1.0: generic converter detected
Mar 14 00:53:08 endeavor kernel: \[  761.974110\] usb 2-1.2: generic converter now attached to ttyUSB0
Mar 14 00:53:08 endeavor kernel: \[  761.974190\] usbserial\_generic 2-1.2:1.1: generic converter detected
Mar 14 00:53:08 endeavor kernel: \[  761.974266\] usb 2-1.2: generic converter now attached to ttyUSB1
Mar 14 00:53:08 endeavor kernel: \[  761.974339\] usbserial\_generic 2-1.2:1.2: generic converter detected
Mar 14 00:53:08 endeavor kernel: \[  761.974417\] usb 2-1.2: generic converter now attached to ttyUSB2
Mar 14 00:53:08 endeavor kernel: \[  761.974489\] usbserial\_generic 2-1.2:1.3: generic converter detected
Mar 14 00:53:08 endeavor kernel: \[  761.974563\] usb 2-1.2: generic converter now attached to ttyUSB3
Mar 14 00:53:08 endeavor kernel: \[  761.974666\] usbserial\_generic 2-1.2:1.4: generic converter detected
Mar 14 00:53:08 endeavor kernel: \[  761.974788\] usb 2-1.2: generic converter now attached to ttyUSB4
Mar 14 00:53:08 endeavor modem-manager\[927\]:   (ttyUSB4) opening serial port...
Mar 14 00:53:08 endeavor modem-manager\[927\]:   (ttyUSB2) opening serial port...
Mar 14 00:53:08 endeavor modem-manager\[927\]:   (ttyUSB1) opening serial port...
Mar 14 00:53:08 endeavor modem-manager\[927\]:   (ttyUSB0) opening serial port...
Mar 14 00:53:08 endeavor modem-manager\[927\]:   (ttyUSB3) opening serial port...
Mar 14 00:53:10 endeavor modem-manager\[927\]:   (ttyUSB2) closing serial port...
Mar 14 00:53:10 endeavor modem-manager\[927\]:   (ttyUSB2) serial port closed
Mar 14 00:53:10 endeavor modem-manager\[927\]:   (ttyUSB2) opening serial port...
Mar 14 00:53:10 endeavor modem-manager\[927\]:   (Generic): CDMA modem /sys/devices/pci0000:00/0000:00:1d.0/usb2/2-1/2-1.2 claimed port ttyUSB2
Mar 14 00:53:10 endeavor modem-manager\[927\]:   (ttyUSB3) closing serial port...
Mar 14 00:53:10 endeavor modem-manager\[927\]:   (ttyUSB3) serial port closed
Mar 14 00:53:10 endeavor modem-manager\[927\]:   (Generic): CDMA modem /sys/devices/pci0000:00/0000:00:1d.0/usb2/2-1/2-1.2 claimed port ttyUSB3
Mar 14 00:53:10 endeavor modem-manager\[927\]:   (ttyUSB2) closing serial port...
Mar 14 00:53:10 endeavor modem-manager\[927\]:   (ttyUSB2) serial port closed

Baris 1 sampai 8 berisikan kernel yang mendeteksi CE81B, lalu awalnya menggunakan modul usb-storage . Pada baris 9 dan seterusnya, kita bisa lihat bahwa kernel memutuskan (disconnect) koneksi ke CE81B, lalu mendeteksi ulang, dan kali ini menggunakan usbserial.

Perhatikan baris 32 dan 35 dimana CE81B diidentikasi sebagai modem CDMA dan dapat diakses melalui ttyUSB2 dan ttyUSB3.

Di titik ini kita sudah berhasil mendeaktif mode penyimpanan, lalu mengaktifkan modem. Sekarang kita akan menggunakan wvdial untuk dapat untuk ber-Internet. Caranya:

➜  ~  sudo wvdialconf
Editing \`/etc/wvdial.conf'.

Scanning your serial ports for a modem.

Modem Port Scan<\*1>: S0   S1   S2   S3   S4   S5   S6   S7   
Modem Port Scan<\*1>: S8   S9   S10  S11  S12  S13  S14  S15  
Modem Port Scan<\*1>: S16  S17  S18  S19  S20  S21  S22  S23  
Modem Port Scan<\*1>: S24  S25  S26  S27  S28  S29  S30  S31  
WvModem<\*1>: Cannot get information for serial port.
ttyUSB0<\*1>: ATQ0 V1 E1 -- failed with 2400 baud, next try: 9600 baud
ttyUSB0<\*1>: ATQ0 V1 E1 -- failed with 9600 baud, next try: 9600 baud
ttyUSB0<\*1>: ATQ0 V1 E1 -- and failed too at 115200, giving up.
WvModem<\*1>: Cannot get information for serial port.
ttyUSB1<\*1>: ATQ0 V1 E1 -- failed with 2400 baud, next try: 9600 baud
ttyUSB1<\*1>: ATQ0 V1 E1 -- failed with 9600 baud, next try: 9600 baud
ttyUSB1<\*1>: ATQ0 V1 E1 -- and failed too at 115200, giving up.
WvModem<\*1>: Cannot get information for serial port.
ttyUSB2<\*1>: ATQ0 V1 E1 -- OK
ttyUSB2<\*1>: ATQ0 V1 E1 Z -- OK
ttyUSB2<\*1>: ATQ0 V1 E1 S0=0 -- OK
ttyUSB2<\*1>: ATQ0 V1 E1 S0=0 &C1 -- OK
ttyUSB2<\*1>: ATQ0 V1 E1 S0=0 &C1 &D2 -- OK
ttyUSB2<\*1>: ATQ0 V1 E1 S0=0 &C1 &D2 +FCLASS=0 -- OK
ttyUSB2<\*1>: Modem Identifier: ATI -- Manufacturer: Haier
ttyUSB2<\*1>: Speed 9600: AT -- OK
ttyUSB2<\*1>: Max speed is 9600; that should be safe.
ttyUSB2<\*1>: ATQ0 V1 E1 S0=0 &C1 &D2 +FCLASS=0 -- OK
WvModem<\*1>: Cannot get information for serial port.
ttyUSB3<\*1>: ATQ0 V1 E1 -- OK
ttyUSB3<\*1>: ATQ0 V1 E1 Z -- OK
ttyUSB3<\*1>: ATQ0 V1 E1 S0=0 -- OK
ttyUSB3<\*1>: ATQ0 V1 E1 S0=0 &C1 -- OK
ttyUSB3<\*1>: ATQ0 V1 E1 S0=0 &C1 &D2 -- OK
ttyUSB3<\*1>: ATQ0 V1 E1 S0=0 &C1 &D2 +FCLASS=0 -- OK
ttyUSB3<\*1>: Modem Identifier: ATI -- Manufacturer: Haier
ttyUSB3<\*1>: Speed 9600: AT -- OK
ttyUSB3<\*1>: Max speed is 9600; that should be safe.
ttyUSB3<\*1>: ATQ0 V1 E1 S0=0 &C1 &D2 +FCLASS=0 -- OK
WvModem<\*1>: Cannot get information for serial port.
ttyUSB4<\*1>: ATQ0 V1 E1 -- failed with 2400 baud, next try: 9600 baud
ttyUSB4<\*1>: ATQ0 V1 E1 -- failed with 9600 baud, next try: 9600 baud
ttyUSB4<\*1>: ATQ0 V1 E1 -- and failed too at 115200, giving up.

Found a modem on /dev/ttyUSB2.
Modem configuration written to /etc/wvdial.conf.
ttyUSB2: Speed 9600; init "ATQ0 V1 E1 S0=0 &C1 &D2 +FCLASS=0"
ttyUSB3: Speed 9600; init "ATQ0 V1 E1 S0=0 &C1 &D2 +FCLASS=0"

Perintah di atas akan mencari modem yang aktif, memberitahu nama file yang terasosiasi dengan modem tersebut, lalu membuatkan konfigurasi awal untuk modem. Konfigurasi tersebut tersedia di /etc/wvdial.conf

Modifikasi berkas wvdial.conf tersebut dengan informasi di bawah ini, cukup ganti nilai atribut modem  sesuai hasil  wvdialconfig di atas:

\[Dialer Defaults\]
Init1 = ATZ
Init2 = ATQ0 V1 E1 S0=0 &C1 &D2 +FCLASS=0
Stupid Mode = 1
Modem Type = Analog Modem
Command Line = ATDT
ISDN = 0
Password = smartfren
New PPPD = yes
Username = smartfren
Phone = #777
Modem = /dev/ttyUSB3
Baud = 460800

Simpan file wvdial.conf tersebut. Lalu jalankan perintah wvdial, seperti di bawah ini:

➜  ~  sudo wvdial                                      
--> WvDial: Internet dialer version 1.61
--> Cannot get information for serial port.
--> Initializing modem.
--> Sending: ATZ
OK
--> Sending: ATQ0 V1 E1 S0=0 &C1 &D2 +FCLASS=0
ATQ0 V1 E1 S0=0 &C1 &D2 +FCLASS=0
OK
--> Modem initialized.
--> Sending: ATDT#777
--> Waiting for carrier.
ATDT#777
CONNECT 9300000
--> Carrier detected.  Starting PPP immediately.
--> Starting pppd at Fri Mar 14 02:28:46 2014
--> Pid of pppd: 10594
--> Using interface ppp0
--> pppd: k\[7f\]
--> pppd: k\[7f\]
--> pppd: k\[7f\]
--> pppd: k\[7f\]
--> local  IP address 10.196.233.0
--> pppd: k\[7f\]
--> remote IP address 10.17.95.21
--> pppd: k\[7f\]
--> primary   DNS address 10.17.118.187
--> pppd: k\[7f\]
--> secondary DNS address 10.17.118.251
--> pppd: k\[7f\]
