# Localization

<a name="introduction"></a>
## แนะนำ

คลาส `Lang` จะทำหน้าที่แปลภาษาในเบื้องต้นให้กับเมนูหรือป้ายกำหับต่างๆ ในเว็บของเรา

<a name="language-files"></a>
## ไฟล์ที่เก็บข้อมูลภาษา

ถูกเก็บไว้ที่โฟลเดอร์ `app/lang` โดยโครงสร้างจะเป็นแบบนี้

	/app
		/lang
			/en
				messages.php
			/es
				messages.php

ไฟล์ที่เก็บภาษาจะเก็บในรูปแบบอาเรย์:

**ตัวอย่างของไฟล์ภาษา**

	<?php

	return array(
		'welcome' => 'Welcome to our application'
	);

โดยค่าเริ่มต้นเเล้ว ค่าภาษาจะถูกกำหนดไว้ที่ `app/config/app.php` แต่ถ้าจะตั้งเราจะตั้งแบบไม่ให้คลุมไปทั้งเว็บก็ใช้เมทอด `App::setLocale`

**ตัวอย่าง**

	App::setLocale('es');

<a name="basic-usage"></a>
## การใช้งานเบื้องต้น

**การดึงค่าจากไฟล์ภาษา**

	echo Lang::get('messages.welcome');

ฟังก์ชัน `get` ใช้ดึงค่าโดยมีพารามิเตอร์คือชื่อภาษาและแถวที่ต้องการดึง



**การส่งพารามิเตอร์ไป**

พารามิเตอร์ที่สองจะใช้รับค่าที่จะส่งมา

	'welcome' => 'Welcome, :name',

 ตัวอย่างการใช้งาน

	echo Lang::get('messages.welcome', array('name' => 'Dayle'));

**ตรวจว่าในไฟล์ภาษามีคอลัมน์นี้อยู่**

	if (Lang::has('messages.welcome'))
	{
		//
	}

<a name="pluralization"></a>
## Pluralization

Pluralization คือโครงสร้างไวยากรณ์ของแต่ละภาษาที่มีความแตกต่างกัน แต่เราใช้เครื่องหมาย ในการสร้างตัวเลือกระหว่างเอกพจน์กับพหูพจน์:

	'apples' => 'There is one apple|There are many apples',

เเล้วเราก็ใช้ฟังก์ชัน `Lang::choice` ในการเลือก

	echo Lang::choice('messages.apples', 10);

เนื่องจากการแปลภาษาของ laravel สืบทอดมาจากของ Symfony เราจึงสามารถสร้างเงื่อนไขที่ซับซ้อนดังตัวอย่างได้

	'apples' => '{0} There are none|[1,19] There are some|[20,Inf] There are many',
