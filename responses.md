# Views & Responses
บทนี้จะมาพูดถึงคลาส views กับ Response นะครับ
<a name="basic-responses"></a>
## Basic Responses

**การส่งค่าคืนแบบง่ายๆ**

	Route::get('/', function()
	{
		return 'Hello World';
	});

**สร้างการส่งกลับเอง**

คลาส `Response` สืบทอดมาจากคลาส `Symfony\Component\HttpFoundation\Response` เราจะมาดูเฉพาะเมทอดที่สำคัญกันนะครับ

ตัวอย่างการสร้างคำตอบกลับนะครับ

	$response = Response::make($contents, $statusCode);

	$response->header('Content-Type', $value);

	return $response;

**เพิ่ม cookie ลงไปในคำตอบกลับ**

	$cookie = Cookie::make('name', 'value');

	return Response::make($content)->withCookie($cookie);

<a name="redirects"></a>
## Redirects การส่งกลับ

**ส่งกลับไปที่ route**

	return Redirect::to('user/login');

**ส่งกลับไปพร้อมกับ ข้อความ**
	
	return Redirect::to('user/login')->with('message', 'Login Failed');

**ส่งกลับไปที่ route ที่มีชื่อย่อตามตัวอย่าง**

	return Redirect::route('login');

**ส่งกลับไปที่ route ที่มีชื่อย่อตามตัวอย่างพร้อมกับค่า**

	return Redirect::route('profile', array(1));

**ส่งกลับไปที่ route ที่มีชื่อย่อตามตัวอย่างพร้อมกับตัวแปร**

	return Redirect::route('profile', array('user' => 1));

**ส่งกลับไปที่ฟังก์ชันใน controller **

	return Redirect::action('HomeController@index');

**ส่งกลับไปที่ฟังก์ชันใน controller พร้อมกับพารามิเตอร์**

	return Redirect::action('UserController@profile', array(1));

**ส่งกลับไปที่ฟังก์ชันใน controller พร้อมกับตัวแปร**

	return Redirect::action('UserController@profile', array('user' => 1));

<a name="views"></a>
## Views

Views คือส่วนที่ใช้เก็บไฟล์ที่ใช้สร้างหน้า html นะครับจะถูกเก็บไว้ที่โฟลเดอร์ `app/views` 
ตัวอย่าง view

	<!-- View stored in app/views/greeting.php -->

	<html>
		<body>
			<h1>Hello, <?php echo $name; ?></h1>
		</body>
	</html>

การใช้งาน view เบื้องต้นครับ

	Route::get('/', function()
	{
		return View::make('greeting', array('name' => 'Taylor'));
	});



**ส่งค่าไปที่ view ครับ**

	$view = View::make('greeting', $data);

	$view = View::make('greeting')->with('name', 'Steve');

ในตัวอย่างตัวแปร `$name`จะถูกใช้งานบน View ได้

You may also share a piece of data across all views:

	View::share('name', 'Steve');

**การส่ง view แทรกเข้าไปในอีก view หนึ่งครับ**

เราสร้างโฟลเดอร์ขึ้นมาเก็บ view ที่เราจะทำเป็น view ย่อยก่อนตัวอย่าง `app/views/child/view.php` ตัวอย่างการใช้งาน

	$view = View::make('greeting')->nest('child', 'child.view');

	$view = View::make('greeting')->nest('child', 'child.view', $data);

ผลที่ออกมาครับ

	<html>
		<body>
			<h1>Hello!</h1>
			<?php echo $child; ?>
		</body>
	</html>

<a name="view-composers"></a>
## View Composers

View composers คือเมทอดที่ช่วยเราในการจัดการค่าที่เราต้องแสดง ในทุกหน้าของ view 
ลดการเขียนโค้ดซ้ำซ้อน

**ตัวอย่างการใช้งาน**

	View::composer('profile', function($view)
	{
		$view->with('count', User::count());
	});

ตอนนี้ทุกครั้งที่ `profile` view ถูกสร้าง `count` จะถูกส่งขึ้นไปด้วย

เราสามารถส่งขึ้นไปทีละหลายๆ view ได้

    View::composer(array('profile','dashboard'), function($view)
    {
        $view->with('count', User::count());
    });

ถ้าเราต้องการทำให้เป็นคลาสเพื่อง่ายต่อการจัดกลุ่ม เราต้องทำแบบนี้ครับ

	View::composer('profile', 'ProfileComposer');

สร้างคลาสขึ้นมา

	class ProfileComposer {

		public function compose($view)
		{
			$view->with('count', User::count());
		}

	}

แล้วอย่าลืมเพิ่มเข้าไปที่ไฟล์ `composer.json` 

<a name="special-responses"></a>
## การส่งกลับแบบพิเศษ

**สร้างการส่งกลับในรูปแบบของ json**

	return Response::json(array('name' => 'Steve', 'state' => 'CA'));

**สร้างการส่งกลับในรูปแบบของ jsonp**

	return Response::json(array('name' => 'Steve', 'state' => 'CA'))->setCallback(Input::get('callback'));

**สร้างการส่งกลับในรูปแบบของการดาวน์โหลดไฟล์**

	return Response::download($pathToFile);

	return Response::download($pathToFile, $name, $headers);
