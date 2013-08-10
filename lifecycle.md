## Laravel Lifecycle

### ตอนที่ 1  Autoloading
มาดูวัฏจักรการทำงานของ Laravel กันครับ มีอยู่ด้วยกัน 4 ช่วง `Autoloading,Bootstrap,Application,Run` เริ่มที่ `Autoloading `ก่อนครับ


1.  เริ่มจากไฟล์ `index.php` ครับ ช่วงที่เราตั้งค่า `vhost `เราจะตั้งให้เส้นทางที่เข้าถึงเริ่มแรก คือ `/public/` พอเริ่มเข้าถึงไฟล์  `index.php` ทำการเรียกไฟล์ `autoload.php` จากโฟลเดอร์ `bootstrap`

1.  ในไฟล์ `autoload.php` จะทำการตั้งค่าเวลาที่ตัวเว็บเริ่มทำงาน
 ![](http://resource.thaicreate.com/upload/stock/20130712081741.jpg?v=1001)





1. จากนั้นเราจะ เรียกไฟล์ `autoload.php` จากโฟลเดอร์ `vendor` ขึ้นมา
![](http://resource.thaicreate.com/upload/stock/20130712081817.jpg?v=1001)
        


1. ที่เห็นชื่อยาวขนาดนี้เป็นเพราะถูกสุมมาจากการที่ใช้คำสั่ง`artisan dump autoload `เพื่อสร้าง รายชื่อของ `package` ใหม่นะครับ แต่ก่อนหน้านั้นเราจะทำการโหลดไฟล์ `package` ทั้งหมดก่อน
โดยเรียกไฟล์ `autoload_real.php` ขึ้นมา
![](http://resource.thaicreate.com/upload/stock/20130712081839.jpg?v=1001)



1. ต่อมาใน ไฟล์ `vendor/composer/autoload_real.php` จะทำการเรียกไฟล์ `Classloader` มาทำการสร้างเป็นวัตถุชื่อ `$loader` แล้วก็
![](http://resource.thaicreate.com/upload/stock/20130712081857.jpg?v=1001)


1. เรียกไฟล์ `autoload_classmap` กับ `autoload_namespace`ขึ้นมาซึ่งในไฟล์ `autoload_classmap` จะเป็นรายชื่อของไฟล์ `package` ทั้งหมดที่อยู่ในโฟลเดอร์ `vendor` ส่วนในไฟล์ `autoload_namespace` จะเป็นรายชื่อของตัว `namespace ` ที่ใช้เรียก `root path` ของไฟล์ `package` นั้นครับ
![](http://resource.thaicreate.com/upload/stock/20130712082030.jpg?v=1001)



1.  พอเรียกเสร็จก็จะส่งกลับมาที่ ไฟล์ `autoload` แล้วก็ส่งค่าต่อไปให้ `ClassLoader` ของ `laravel` 
![](http://resource.thaicreate.com/upload/stock/20130712082241.png?v=1001)
1. ตามลงมาดูว่า `Classloader`ของ `laravel` มันทำยังไงต่อนะครับ
   ฟังก์ชัน `register`ทำการเรียกใช้ ฟังก์ชัน `load `
![](http://resource.thaicreate.com/upload/stock/20130712082325.png?v=1001)
 


1. ฟังก์ชัน `load`จะทำการเรียกโฟลเดอร์ `package` ที่มีตามรายชื่อในตัวแปร `class`ที่ส่งมาตอนแรก
![](http://resource.thaicreate.com/upload/stock/20130712082739.png?v=1001)



1. เท่านี้ก็เสร็จการโหลด `package` ในโฟลเดอร์ `vendor` ครับ อะแต่ยังไม่หมดนะครับ ยังเหลือ package ที่โฟลเดอร์ workbench   เรียกใช้คลาส `Starter`เพื่อทำการลงทะเบียน `package` ในโฟลเดอร์ `workbench`
![](http://resource.thaicreate.com/upload/stock/20130712082901.jpg?v=1001)

โฟลเดอร์ workbech คือส่วนที่เราใช้ในการพัฒนา package ในเครื่องเราซึ่งยังไม่เสร็จหรือว่าทำขึ้นใช้เอง เราก็จะมาเริ่มต้นจากตรงนี้
 	
 package ถือว่าเป็นส่วนสำคัญที่จะต้องรู้ก่อนใน laravel 4 นี้เลย การเขียน package เป็นจะทำให้เราเลือก php libraly ตัวไหนก็ได้บน packagist.org ซึ่งเป็นศูนย์รวมของ libraly ของ php ที่ใช้มาตรฐาน psr-0,psr-1,psr-2  มาสร้างเป็น pakage  ใช้เอง ซึ่งผมก็ได้ศึกษาแล้วลองทำไปเเล้วหลายตัวครับ ไว้จบเรื่องนี้ เเล้วจะมาขียนเรื่องการสร้าง package ต่อครับ 

ปล.ตัวแปร Laravel_Start ตอนแรกเราสามารถนำไปใช้ ลบกับค่าเวลาปัจจุบันเพื่อหาเวลาที่เว็บใช้ จัดการคำร้องขอ 


----------
           
### ตอนที่ 2 Application



1. ขั้นตอนนี้จะเป็นการเริ่มสร้างตัวออปเจคหลักของ เว็บกันแล้วนะครับ
เริ่มจากไฟล์ `bootstrap/start.php` เราจะดำดิ่งลงไปดูในคลาส ` Application` 
![](http://resource.thaicreate.com/upload/stock/20130718073801.png?v=1001)
 ตรงคลาสนี้เป็นตัวหลักที่คอยขับเคลื่อนเว็บไซต์ของเราเลยครับ เพราะ มีการเรียกใช้ คลาสเข้ามาจำนวนมาก
ฟังก์ชันในนี้ ผมจะไม่พยายามไปดูให้ตาลาย ให้รู้ว่าเรา มีฟังก์ชันที่เราต้องเข้าใจ คือ 
`App::share,App::bind,App::instance,App::make` เราไปดูต่อว่าเริ่มมาตัว `class Application`ทำอะไรต่อ

1. เนื่องจาก ทำการเรียกตัวคลาสเข้ามาแล้ว จึงทำการสร้างวัตถุใหม่ได้ทันทีเลยครับ สิ่งที่ต้องสร้างก่อนสามอันดับแรกเลยก็คือ `Class Exception` ใช้ในการจัดการข้อผิดพลาด
`Class Routing` จัดการๆวิ่งของคำร้องขอ
`Class Event` จัดการเหตุการณ์ต่าง ที่เกิดขึ้น
![](http://resource.thaicreate.com/upload/stock/20130718073947.png?v=1001)

1. กลับมาที่ ไฟล์ `start.php` ครับ ขั้นต่อมา เราจะทำการตรวจสถานะการทำงานของระบบนะครับ
ว่าอยู่ในโหมดไหน ตรงนี้มีประโยชน์มากในกรณีเมื่อเรากำลังพัฒนาอยู่ก็ตั้งให้เป็น `development` ส่วนจะไปกำหนดที่ไหนค่อยมาพูดกันอีกทีครับ
![](http://resource.thaicreate.com/upload/stock/20130718074104.png?v=1001)

1. ทำการโหลดไฟล์ `path.php` ซึ่งจะเป็น `class `ใช้ในการกำหนดเส้นทางการเข้าหาโฟลเดอร์ต่างๆ
![](http://resource.thaicreate.com/upload/stock/20130718074232.png?v=1001)

1. ทำการเรียกใช้งานตัว `laravel` ละครับตรงนี้จะเป็นการ เรียกไฟล์ `start` ของตัว `laravel` ขึ้นมา
![](http://resource.thaicreate.com/upload/stock/20130718074344.png?v=1001)

----------

### ตอนที่ 3 Bootstrap

1. laravel เริ่มการตรวจว่ามีการติตตั้ง `Mcrypt` ไหม
![](http://i.imgur.com/ptIiszw.png)

1. ต่อมาก็ตรวจสภาวะการตั้งค่าก่อนว่าเรากำลัง ตั้งให้อยู่ในสถานะการทอบสอบไหม
  ![](http://i.imgur.com/ZAvlT5S.png)

1. คลาส `Facade` จะทำการเคลียร์ ตัวแทนของมัน
 แล้วก็สร้างขึ้นใหม่
![](http://i.imgur.com/iUA5IwB.png)

1. ทำการโหลดข้อมูลการตั้งค่าจากตัวแปร `$env` ขึ้นมาใส่ให้คลาส `Config` ครับ แล้วสร้างตัวแทนให้คลาส `Config`
![](http://i.imgur.com/xDX8G4H.png)

1. ทำการตั้งค่าวันเวลาโดยโหลดค่ามาจากตัวแปรในไฟล์ `app.php`
![](http://i.imgur.com/Ltc5Oyz.png)

1. โหลดค่าชื่อย่อจากที่ไฟล์ `app.php` มา
![](http://i.imgur.com/ZV5D44o.png)

 มาให้ฟังก์ชัน `getInstance` มาทำการลงทะเบียนไว้
 ![](http://i.imgur.com/Ir4ihzX.png)

1. ทำการอนุญาตให้ใช้คำร้องขอชนิด `put` ใช้ทำการแก้ไขข้อมูล กับ `delete`  ใช้ในการลบข้อมูล เพื่อใช้ตอนทำ `restful` ในกรณีที่ `firewall` ไม่อนุญาตุให้เมทอดสองอันนี้ผ่าน เราจึงจำเป็นต้องทำการเปลี่ยนส่วนหัวของเมทอด `post` ให้กลายเป็นสองเมทอดที่โดนบล็อก
 ![](http://i.imgur.com/mQlXv2q.png)

1. ทำการวมคลาสทุกตัวที่โหลดมาเข้าด้วยกัน `$config['providers']` คือรายชื่อคลาสที่อยู่ในไฟล์ `app.php`
![](http://i.imgur.com/ulQDYrx.png)

1. เรียกใช้ฟังก์ชัน  `$app->boot()` เพื่อเริ่มต้นการทำงาน เเล้วเรียกไฟล์  `global.php` ซึ่งเป็นไฟล์ที่เราสามารถใช้กำหนดค่าเริ่มต้นเองได้
![](http://i.imgur.com/atM8b1g.png)

1. รองสุดท้ายดึงไฟล์ที่เราใช้กำหนดสภาวะการตั้งค่าขึ้นมา
 ![](http://i.imgur.com/w6HpkVH.png)

1. สุดท้ายในบทนี้แล้วครับเรียกไฟล์ `routes.php` จบตอนครับ


### ตอนที่ 4 Run
ตอนสุดท้ายแล้วครับ เราใกล้ได้คำตอบกลับหรือ `Response` 
 

1. ทำการเขียน `session`
1. ค้นหา `Route` ที่ส่งมาว่าตรงกับตัวที่่กำหนดไว้ไหม
1. สั่งให้ `filter` เช่น `before`,`after` ทำงาน
1. สร้างคำตอบกลับหรือ `Response` 
1.  ส่งคำตอบกลับ
1. เรียกใช้ `after filter` อย่างเช่น `log` 

จบแล้วครับกว่าจะได้คำตอบกลับหนึ่งครั้งยาวนานไหมครับ

ที่มา :: [Kenny Mayer Per Your Request](http://www.youtube.com/watch?v=bRyOrtCim70 )