# Errors & Logging

<a name="error-detail"></a>
## การแสดง Error 

โดยค่าเริ่มต้นเเล้วการแสดงข้อผิดพลาดจะถูกเปิดใช้งานอยู่เเล้ว หากเราพัฒนาเว็บเสร็จ ก่อนจะส่งขึ้นโฮสติ้งก็ควรเข้าไปตั้งค่าตัวแปร `debug` ที่ `app/config/app.php` ให้เป็น `false`

<a name="handling-errors"></a>
## การจัดการ Errors

โดยค่าเริ่มต้นเเล้วการจัดการข้อผิดพลาดต่างๆเราจะทำในไฟล์ `app/start/global.php` 

	App::error(function(Exception $exception)
	{
		Log::error($exception);
	});

ถ้าเราอยากกำหนดการทำงานหลังจากเกิด `RuntimeException` เราก็ทำได้ดังตัวอย่างครับ

	App::error(function(RuntimeException $exception)
	{
		// Handle the exception...
	});

เมื่อเราใช้การ return ในฟังก์ชันข้างล่างนี้ ค่าจะถูกส่งกลับไปยังบราวเซอร์เสมอครับ:

	App::error(function(InvalidUserException $exception)
	{
		Log::error($exception);

		return 'Sorry! Something is wrong with this account!';
	});

การดักรอ PHP fatal errors เราใช้ฟังก์ชัน `App::fatal` 

	App::fatal(function($exception)
	{
		//
	});

<a name="http-exceptions"></a>
## HTTP Exceptions

กรณีข้อผิดพลาดที่เกิดขึ้นเพราะไม่พบหน้าที่เรียก (404), กับไม่มีสิทธิเข้าถึง (401) เราสามารถแก้ไขค่าที่จะไปแสดงได้ดังนี้

	App::abort(404, 'Page not found');

	App::abort(401, 'You are not authorized.');
ค่า 404 คือรหัสข้อผิดพลาดครับ

<a name="handling-404-errors"></a>
## การจัดการข้อผิดพลาด 404 

เราสามารถสร้างหน้าแสดงข้อผิดพลาดที่เรา สามารถออกแบบได้เอง แล้วต้องมาตั้งค่าแบบในตัวอย่างนี้ พารามิเตอร์ที่หนึ่งคือ ที่อยู่ของไฟล์ view 

	App::missing(function($exception)
	{
		return Response::view('errors.missing', array(), 404);
	});

<a name="logging"></a>
## การเก็บ log

คลาสที่ใช้ในการเก็บ log ของ laravel เป็นการใช้คลาส [Monolog](http://github.com/seldaek/monolog) มาพัฒนาต่อ Laravel ตั้งค่าเริ่มต้นให้เก็บ log ทุกวันแล้วส่งไปเก็บไว้ใน `app/storage/logs`เราสามารถกำหนดค่า log ที่จะเขียนได้ดังตัวอย่าง

	Log::info('This is some useful information.');

	Log::warning('Something could be going wrong.');

	Log::error('Something is really going wrong.');

ประเภทของ log ถูกประกาศไว้ในมาตรฐาน [RFC 5424](http://tools.ietf.org/html/rfc5424) มี **debug**, **info**, **notice**, **warning**, **error**, **critical**, และ **alert**.

ตัวอย่างการส่งค่าที่เป็นอาเรย์ให้ฟังก์ชัน info ที่อยู่ในคลาส log

	Log::info('Log message', array('context' => 'Other helpful information'));

การดึงค่ามาใช้ก็ใช้ฟังก์ชันตามตัวอย่างเลยครับ

	$monolog = Log::getMonolog();

เราสามารถกำหนดเหตุการณ์ที่เราจะให้เก็บ log ได้โดยใช้ฟังก์ชัน listen:

**ตัวอย่าง**

	Log::listen(function($level, $message, $context)
	{
		//
	});
