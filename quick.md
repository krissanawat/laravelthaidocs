# การเริ่มต้นอย่างรวดเร็ว

> หมายเหตุ :: url ที่เราจะเรียกใช้งานในตอนเริ่มแรกคือ localhost หรือ 127.0.0.1
> ตามด้วยชื่อโฟลเดอร์ของเว็บที่เราสร้าง แล้วตามด้วย public นะครับ ยกตัวอย่าง 127.0.0.1/taqmaninw/public นะครับ  

บททนี้จะทำให้เราเห็นภาพรวมของ laravel นะครับ

<a name="installation"></a>
## การติดตั้ง

ถ้าไม่รู้จัก composer แนะนำไปอ่าน [ที่นี้](http://taqmaninw.com/composer-คืออะไร) ก่อน เริ่มจากใช้คำสั่ง

	composer create-project laravel/laravel ชื่อโปรเจค --prefer-dist

หลังจากนั้น composer จะทำการดาวน์โหลดไฟล์ต่างๆ มาเก็บที่โฟลเดอร์ที่เรากำหนดชื่อไว้

หลังจากนั้นก็ทำความรู้จักกับ [โครงสร้างโฟลเดอร์ของ laravel](http://taqmaninw.com/ทำความรู้จักโครงสร้างโฟลเดอร์ของ-laravel-4) เริ่มแรกเราต้องไปกำหนดค่าต่างๆ ที่โฟลเดอร์ `app/config` ก่อนในนี้ก็จะมีการตั้งค่าให้มากมายแต่เราอาจต้องการแค่ [การกำหนดค่าเบื้องต้น](http://taqmaninw.com/การตั้งค่าเบื้องต้นให้กับ-laravel)

<a name="routing"></a>
## Routing
เราต้องกำหนด url ที่เราจะอนุญาตให้เข้าถึง ฟังก์ชันต่างก่อนที่ `app/routes.php` ตัวอย่างการสร้าง Route เบื้องต้น

	Route::get('users', function()
	{
		return 'Users!';
	});

ตอนนี้เมื่อเราพิมพ์ชื่อโปรเจคของเราบนบราวเซอร์แล้วตามด้วย `/users` เราจะเห็นคำว่า `Users!` แสดงอยู่

การสร้าง Route ไปหา Controller

	Route::get('users', 'UserController@getIndex');

ตอนนี้ `/user` จะถูกส่งไปที่ฟังก์ชน getIndex ของ UserController  

<a name="creating-a-view"></a>
การสร้าง View

ต่อมาเรามาสร้างไฟล์ที่จะใช้จัดการรูปแบบในการแสดงผลที่โฟลเดอร์ `app/views` เราสร้างไฟล์ที่ชื่อ `layout.blade.php` และ `users.blade.php`ต่อด้วย `layout.blade.php` 

	<html>
		<body>
			<h1>Laravel Quickstart</h1>

			@yield('content')
		</body>
	</html>

ต่อมาในไฟล์ `users.blade.php` เราจะดึงไฟล์ layout มาลง

	@extends('layout')

	@section('content')
		Users!
	@stop

เพื่อความไม่งงว่าตัวแปรเหล่านี้คืออะไร ตามไปดูที่นี่เลยครับ [Blade documentation](/docs/templates) 

ถ้าเราจะแสดงไฟล์ view ที่เราสร้างก็ต้องอาศัย Route ดังตัวอย่าง

	Route::get('users', function()
	{
		return View::make('users');
	});

ต่อไปเราจะไปลุยดาต้าเบสกันนะครับ

<a name="creating-a-migration"></a>
## การทำ Migration

เราจะใช้คลาส [Migration](#migration) ในการจัดการฐานข้อมูลนะครับ
เริ่มต้นด้วยการตั้งค่าในการเชื่อมต่อฐานข้อมูลก่อนที่ไฟล์  `app/database` ในค่าเริ่มต้นเเล้วอาเรย์ `driver`จะเป็น `mysql` แล้วเราก็เปลี่ยนค่าตรง `mysql` เป็นข้อมูลในการเชื่อมต่อฐานข้อมูลของเรา

ต่อมาในการสร้าง migration เราใช้ คำสั่ง artisan ใน commandline จากในโฟลเดอร์โปรเจคของเรา ตัวอย่าง

	php artisan migrate:make create_users_table

ต่อมาไฟล์ migrration จะไปโผล่ที่โฟลเดอร์ `app/database/migrations` ในไฟล์จะมีฟังก์ชัร `up` และ `down` เราจะสร้าง [Schema](#schema-builder) เพื่อการจัดการฐานข้อมูล

ตัวอย่างการสร้าง [Schema](#schema-builder)

	public function up()
	{
		Schema::create('users', function($table)
		{
			$table->increments('id');
			$table->string('email')->unique();
			$table->string('name');
			$table->timestamps();
		});
	}

	public function down()
	{
		Schema::drop('users');
	}

ต่อมาเราก็รันคำสั่ง

	php artisan migrate

ถ้าอยากย้อนคำสั่ง `migrate` เราต้องใช้คำสั่ง `migrate:rollback`

<a name="eloquent-orm"></a>
## Eloquent ORM

[Eloquent](#eloquent-oRM) คือ ชุดคำสั่งที่เราใช้ในการทำ sql query นั่นเองครับ ช่วยให้เราสะดวกสะบาย ทำงานได้รวดเร็วขึ้น เริ่มแรกเราต้องไปสร้าง `model ` ที่โฟลเดอร์ `app/models` โดยตั้งชื่อว่า `User.php` ตัวอย่างการประกาศคลาสในโมเดล

	class User extends Eloquent {}

ตัวอย่างการเรียกใช้ Eloquent Model ครับ 

	Route::get('users', function()
	{
		$users = User::all();

		return View::make('users')->with('users', $users);
	});

เมทอด `all`ที่ต่อจากเนมสเปซ `User` จะคิวรี่ค่าทั้งหมดจากตาราง `users` ส่วนใน `View` เราใช้ฟังก์ชัน `with` เพื่อดึงเฉพาะคอลัมน์ user ครับ


<a name="displaying-data"></a>
## Displaying Data

ตัวอย่างการแสดงค่าที่มาจากฐานข้อมูลบนไฟล์ view ครับ

	@extends('layout')

	@section('content')
		@foreach($users as $user)
			<p>{{ $user->name }}</p>
		@endforeach
	@stop


ต่อไปเราต้องไปเรียนรู้เรื่อง [Eloquent](#eloquent-orm) และ [Blade](#templates). หรือแวะเข้าไปอ่านเล่นๆ ก่อนที่เรื่อง[Queues](#queues) และ [Unit Testing](#testing).ถ้าต้องการใช้งานในระดับสูงต่อก็ไปที่ [IoC Container](#ioc).