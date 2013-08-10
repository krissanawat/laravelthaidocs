# Installation
การติดตั้ง laravel

<a name="install-composer"></a>
## Install Composer

Laravel ใช้ [Composer](http://getcomposer.org) ในการจัดการไลบราลี่ต่างๆ รวมจนถึงคลาสหลักของระบบ เริ่มแรกเราต้องไปโหลด `composer.phar`. เราจะได้ไฟล์ที่มีนามสกุลเป็น phar มาแล้วเอาไปวางไว้ที่ `usr/local/bin` เพื่อให้ระบบมองเห็น บนวินโดเรามี [Windows installer](https://getcomposer.org/Composer-Setup.exe) อยากรู้เพิ่มเติมเข้าไปดูได้[บล็อก](http://taqmaninw.com/ccomposer-ผู้กอบกู้แห่งอาณาจักร-php)ของผมได้ครับ

<a name="install-laravel"></a>
## การติดตั้ง Laravel

### ใช้ Composer ในการติดตั้ง

พิมพ์คำสั่งข้างล่างไปที่ commandline composer จะทำการดาวน์โหลดมาลงตรงที่เราเรียกใช้

	composer create-project laravel/laravel

### ดาวน์โหลดเอง

เมื่อติดตัง [laravel เวอร์ชันล่าสุด](https://github.com/laravel/laravel/archive/master.zip) แล้วก็แตกไฟล์ไปลงที่โฟลเดอร์ของ server เปิด command line เลือกที่อยู่ให้ตรงกับที่เอา laravel ไปวางแล้วรันคำสั่ง `php composer.phar install` (หรือ `composer install`) ก็เสร็จสิ้น

ถ้าเราต้องการอัพเดทก็ใช้คำสั่ง `php composer.phar update` 

<a name="server-requirements"></a>
## ความต้องการของระบบ

- PHP >= 5.3.7
- MCrypt PHP Extension

<a name="configuration"></a>
## การตั้งค่า

laravel ไม่ได้ต้องการปรับแต่งอะไรมากเพียงแค่เราเข้้าไปที่ `app/config/app.php` โดยอาจปรับแค่ `timezone` กับ `locale` 

> **Note:** และมีค่า `key` ที่อยู่ใน `app/config/app.php`. เราต้องใช้คำสั่ง `php artisan key:generate`เพื่อสร้างคีย์ที่จะใช้สร้าง private key ในการสร้างรหัสผ่าน hash ในระบบ

<a name="permissions"></a>
### สิทธิ
laravel ต้องการสิทธิในการอ่านเขียนโฟลเดอร์ app/storage 

<a name="paths"></a>
### เส้นทาง

การกำหนดเส้นทางสามารถทำได้ที่ `bootstrap/paths.php` 


<a name="pretty-urls"></a>
## URLs ที่สวยงาม

laravel เตรียมไฟล์ `public/.htaccess` ที่อนุญาตให้เราเรียกใช้งานโดยไม่ต้องใส่ `index.php`. โดยต้องการใช้งานขอ `mod_rewrite` บน server ก่อน

