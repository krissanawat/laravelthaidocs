# Package Development

<a name="introduction"></a>
## คำอธิบายเบื้องต้น

Packages คือการนำไลบรารี่ภายนอกเข้ามาใช้งานใน laravel ซึ่งส่วนมากจะอยู่บน [Packagist](http://packagist.org) และส่วนมากอีกไม่ได้ปรับแต่งให้เข้ากับ framework ไหนเป็นพิเศษ
ซึ่งตรงนี้เราก็ต้อง ออกเเรงกันหน่อยครับ ถ้าอยากจะมี package เป็นของเราเอง
ส่วน [การติดตั้ง package](http://taqmaninw.com/การติดตั้ง-package-บน-laravel-4) ถ้ามาเขียนในนี้คงจะยาว ผมเลยเขียนไว้ที่เว็บแล้ว อยากรู้ต้องทำไงต้องตามไปดูเลยครับ

<a name="creating-a-package"></a>
## การสร้าง Package

สถานที่ๆเราจะทำการพัฒนาคือโฟลเดอร์ชื่อ `workbench` ครับ แต่ตอนแรกเราต้องเข้าไปตั้งค่าที่ไฟล์ `app/config/workbench.php` ในไฟล์นี้เราต้องเปลี่ยน `name` และ `email` ซึ่ง `composer.json` จะนำไปใส่เวลาเราสร้าง package ขึ้นมา

**สร้างโครงของ package ขึ้นมาด้วย CommandLine**

	php artisan workbench vendor/package --resources

parameter แรก `vendor` คือชื่อผู้พัฒนา `package` คือชื่อของ `package` `resources` เป็นพารามิเตอร์ที่บอกให้สร้าง `migrations`, `views`, `config`, ด้วย

เมื่อคำสั่งข้างบนทำงาน `package`ของเราจะไปปรากฏที่โฟลเดอร์ `workbench` ต่อมาเราจะสร้าง `ServiceProvider` ซึงการตั้งชื่อจะเป็นแบบนี้ `[Package]ServiceProvider` ส่วนการนำไปลงททะเบียนที่ไฟล์ `app.php` นั้นเส้นทางของไฟล์จะเป็นแบบนี้ครับ `Taylor\Zapper\ZapperServiceProvider` ซึ่งต้องนำไปวางไว้ที่อาเรย์ชื่อ `providers` 

ก่อนที่จะเริ่มพัฒนา package ของเรา ควรมารู้จักโครงสร้างของมันก่อนครับ

<a name="package-structure"></a>
## โครงสร้างของ package


**โครงสร้างโฟลเดอร์ของ package**

	/src
		/Vendor
			/Package
				PackageServiceProvider.php
		/config
		/lang
		/migrations
		/views
	/tests
	/public

เมื่อตามเข้าไปดูในโฟลเดอร์ `src/ชื่อของเรา/ชื่อpackageของเรา` จะเจอกับไฟล์ `ServiceProvider`กับโฟลเดอร์ `config`, `lang`, `migrations`, และ `views` ซึ่งจะเหมือนในตัวโฟลเดอร์ app หลักของเราแต่ย่อส่วนลงมาครับ

<a name="service-providers"></a>
## Service Providers

เริ่มแรกในไฟล์ `Service Provider` จะมีฟังก์ชัน `boot` และ `register`. ซึ่งในนี้เราจะสามารถ ใส่ฟังก์ชันอะไรก็ได้ที่จะช่วยจัดการ package ของเรา เช่น ดึงไฟล์ route เข้ามา,ใช้ IOC ดึงคลาสอื่นเข้ามาช่วยเพิ่ม event เข้ามาดักฟัง

ฟังก์ชัน `register` จะเริ่มทำงานทันทีเมื่อ package เริ่มทำงาน, ถ้าเรา package ของเราต้องการคลาสอื่นๆ ในการทำงานร่วมด้วยต้องใช้เมทอด `boot` ครับ

โดยค่าเริ่มต้นเเล้วฟังก์ชัน `boot` จะมีค่ามาให้ดังนี้

	$this->package('vendor/package');
    // ตัวอย่างถ้ามีคลาสที่ต้องใช้ร่วมระหว่างการ boot package
	ClassLoader::addDirectories(array(
    		app_path().'/widgets'
    	));

ถ้าลบบรรทัดแรกออกไปผลก็คือ package ไม่ทำงานครับ laravel จะไม่รู้จัก package นี้เลย

<a name="package-conventions"></a>
## Package Conventions

ตัวอย่างการเรียกใช้งานค่าต่างๆใน package

**เรียก view ของ package**

	return View::make('package::view.name');

**เรียกค่าของการตั้งค่า**

	return Config::get('package::group.option');

> **หมายเหตุ:** ถ้า package ของเรามี migrations ควรใสชื่อ package เข้าไปอยู่ไฟล์ด้วย เพื่อป้องกันการเหมือนกับ packages อื่น

<a name="development-workflow"></a>
## ขั้นตอนในการพัฒนา

พอสร้างโปรเจคเสร็จ เราก็ใช้คำสั่ง `php artisan dump-autoload` เพื่อสร้างไฟล์ที่บอกที่อยู่ของ package ของเราขึ้นมา

**ตัวอย่าง**

	php artisan dump-autoload

<a name="package-routing"></a>
## Package Routing

การที่จะโหลดไฟล์ route เข้ามาใช้ต้องใช้ฟังก์ชัน `include` ในฟังก์ชัน `boot` ครับ

**ตัวอย่างการดึง route มาใช้ใน Service Provider**

	public function boot()
	{
		$this->package('vendor/package');

		include __DIR__.'/../../routes.php';
	}

> **หมายเหตุ:**ถ้าต้องการใช้ controller ด้วยมั่นใจว่ามันต้องถูกเพิ่มไว้ในไฟล์ `composer.json` ในส่วน autoload

<a name="package-configuration"></a>
## การตั้งค่า Package 

บาง package เราต้องดึงค่าต่างๆ เช่น appid,appsecret มาใช้ โดยเริ่มต้นไฟล์ `config.php` จะอยู่ในตัว package กรณีที่ดึงลงมาด้วย composer แล้วติดตั้งเสร็จไฟล์ จะอยู่ที่โฟลเดอร์ `/app/config/package`

**การเข้าถึงไฟล์ config**

	Config::get('package::file.option');

ตัวพารามิเตอร์แรกคือชื่อของ package หลังจาก semicolon คือชื่อไฟล์ หลังเครื่องหมายดอทคือชื่อ อาเรย์ `config.php`. ถ้าโฟลเดอร์ที่ใช้เก็บไฟล์ config ของ package มีไฟล์เดียวก็ไม่ต้องใส่ชื่อก็ได้ครับ

**ตัวอย่างกรณีโฟลเดอร์มีไฟล์เดียว**

	Config::get('package::option');

บางครั้งไฟล์ที่ต้องใช้ใน package มันก็ดันไมได้อยู่ใน package สามารถใช้ฟังก์ชัน `addNamespace` ซึ่งมีอยู่ในคลาส `View`, `Lang`, กับ `Config`ดังต้วอย่างเลยครับ


	View::addNamespace('package', __DIR__.'/path/to/views');

ทีนี้เราก็สามารถใช้งานคลาส View ใน package ได้ละ

	return View::make('package::view.name');


### Cascading Configuration Files

เมื่อเราดาวน์โหลดไฟล์ package มาด้วย composer ต้องนำไฟล์โฟลเดอร์ config ของ package ออกมาใช้ก่อน

**ตัวอย่างการใช้งาน**

	php artisan config:publish vendor/package

เมื่อคำสั่งทำงานเสร็จจะปรากฎอยู่ที่ `app/config/packages/vendor/package` 


<a name="package-migrations"></a>
## Package Migrations

เราสามารถทำ migration ให้กับ package โด้โดยใช้พารามิเตอร์ `--bench`  ตามตัวอย่างเลยครับ

**สร้าง migration ให้ package ในโฟลเดอร์ workbench**

	php artisan migrate:make create_users_table --bench="vendor/package"

**สั่งให้ migration ของ package ทำงาน**

	php artisan migrate --bench="vendor/package"

เมื่อพัฒนาเสร็จอยากจะลองใช้งานจริงเองก่อนเราต้องใช้พารามิเตอร์  `--package`เพื่อส่ง package ไปไว้ที่โฟลเดอร์ `vendor` 

**ตัวอย่างคำสั่งการย้าย package ไปไว้ที่ vendor**

	php artisan migrate --package="vendor/package"

<a name="package-assets"></a>
## Package Assets

บาง package จะมีไฟล์ JavaScript, CSS, และ images.เราไม่สามารถสร้างลิ้งโดยตรงจากโฟลเดอร์ `vendor` หรือ `workbench` ฉะนั้นเราต้องย้ายไปไว้ที่ โฟลเดอร์ `public` 

**การย้ายไฟล์ css,js,image โดยใช้ commandline**

	php artisan asset:publish

	php artisan asset:publish vendor/package

แต่ถ้า package ยังอยู่ในโฟลเดอร์ workbench ต้องใช้คำสั่งนี้ครับ

	php artisan asset:publish --bench="vendor/package"

ตอนนี้โฟลเดอร์จะถูกส่งไปไว้ที่ โฟลเดอร์`public/packages` ตัวอย่างชื่อโฟลเดอร์ `userscape/kudos` ตัวอย่างเส้นทางที่อยุ่ `public/packages/userscape/kudos`. 

<a name="publishing-packages"></a>
## การแบ่งปัน Packages ของเรา

เมื่อพัฒนาเสร็จแล้ว เราเกิดอยากให้คนอื่นช่วยพัฒนาต่อ หรืช่วยหาบั๊กให้ เราต้องอัพ package ของเราขึ้น github หรือ bitbucket ก่อนครับ หลังจากนั้นไปสมัคร [Packagist](http://packagist.org) เพื่อนำ package ของเราไปผูกไว้กับฐานข้อมูลหลักของ composer หลังจากนั่น ก็ใช้ composer โหลดลงมาใช้เลย


