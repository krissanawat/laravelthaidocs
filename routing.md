# Routing

ใช้ในการกำหนดว่าเมื่อเราเรียกลิ้งนี้จะให้ทำอะไรขึ้นบ้าง
<a name="basic-routing"></a>
## Basic Routing

ในการตั้งค่าเราจะไปที่ `app/routes.php` โดยรูปแบบของฟังก็ชันที่เป็น Route จะเป็นแบบ Closure callback Closure คืออะไรตามไปตามเข้าไปอ่าน [ที่นี่ครับ](http://taqmaninw.com/Closure-ฟังก์ชันนิรนามของ-php)

**การรับค่าที่เป็น get**

	Route::get('/', function()
	{
		return 'Hello World';
	});

**การรับค่าที่เป็น POST**

	Route::post('foo/bar', function()
	{
		return 'Hello World';
	});

**กำหนด route ในการเรียกพารามิเตอร์ foo ในทุกรูปแบบเมทอด**

	Route::any('foo', function()
	{
		return 'Hello World';
	});

**ลิ้งที่เรียกมาต้องเป็น https เท่านั้น**

	Route::get('foo', array('https', function()
	{
		return 'Must be over HTTPS';
	}));



<a name="route-parameters"></a>
## Route Parameters

ตัวอย่างการกำหนดรูปแบบของพารามิเตอร์

	Route::get('user/{id}', function($id)
	{
		return 'User '.$id;
	});

**พารามิเตอร์แบบมีหรือไม่มีก็ได้**

	Route::get('user/{name?}', function($name = null)
	{
		return $name;
	});

**กำหนดพารามิเตอร์แบบตายตัว**

	Route::get('user/{name?}', function($name = 'John')
	{
		return $name;
	});

**การใช้ regex ตรวจสอบว่าพารามิเตอร์ตรงกับที่กำหนดไว้ไหม**

	Route::get('user/{name}', function($name)
	{
		//
	})
	->where('name', '[A-Za-z]+');

	Route::get('user/{id}', function($id)
	{
		//
	})
	->where('id', '[0-9]+');

จะใส่ไปเป็นอาเรย์ก็ได้

	Route::get('user/{id}/{name}', function($id, $name)
	{
		//
	})
	->where(array('id' => '[0-9]+', 'name' => '[a-z]+'))

<a name="route-filters"></a>
## Route Filters

คือการกำหนดฟังก์ชันที่ใช้ในการตรวจสอบข้อมูล  `auth` ใช้ตรวจว่ามีการล็อกอินไหม,   `guest` ตรวจว่ายังไม่ได้ล็อกอิน, และ `csrf`ตรวจว่าเป็นการทำ csrf ไหม.ซึ่งเราจะไปประกาศไว้ที่ `app/filters.php`

**ตัวอย่างการสร้าง filter**

	Route::filter('old', function()
	{
		if (Input::get('age') < 200)
		{
			return Redirect::to('home');
		}
	});



**การใส่ filter ให้ route**

	Route::get('user', array('before' => 'old', function()
	{
		return 'You are over 200 years old!';
	}));

**การใส่ route หลายตัว**

	Route::get('user', array('before' => 'auth|old', function()
	{
		return 'You are authenticated and over 200 years old!';
	}));

**การกำหนดค่าเฉพาะให้ filter**

	Route::filter('age', function($route, $request, $value)
	{
		//
	});

	Route::get('user', array('before' => 'age:200', function()
	{
		return 'Hello World';
	}));

filter บางตัวเราสั่งให้ทำงานหลังจากที่ route ทำงานไปแล้วเราต้องกำหนดตัวแปร `$response` เพื่อกำหนดค่าที่จะส่งไปให้ตัวฟังก์ชันด้วย

	Route::filter('log', function($route, $request, $response, $value)
	{
		//
	});

**Pattern Based Filters**

เราสามารถกำหนด filter ให้ทำงานเฉพาะเมื่อมีการเรียกตรงกับที่เรากำหนดได้ ตามตัวอย่างเลยครับ.

	Route::filter('admin', function()
	{
		//
	});

	Route::when('admin/*', 'admin');

ตามตัวอย่างเราเพิ่ม filter ชื่อ `admin` เข้ากับทุกลิ้งที่มี `admin/` อยู่ข้างใน

แล้วก็ยังสามารถกำหนดเมทอดให้ได้ด้วย

	Route::when('admin/*', 'admin', array('post'));

**Filter Classes**

ในการกรองขั้นสูงเราสามารถสร้างคลาสขึ้นมาได้เอง แล้วทำการใช้ [IoC Container](/docs/ioc) เรียกใช้คลาสนั้น

**ตัวอย่างคลาส**

	class FooFilter {

		public function filter()
		{
			// Filter logic...
		}

	}

**ลงทะเบียนคลาสโดยให้ชื่อที่จะนำไปใช้ว่า foo**

	Route::filter('foo', 'FooFilter');

<a name="named-routes"></a>
## Named Routes

คือการตั้งชื่อย่อให้กับ route:

	Route::get('user/profile', array('as' => 'profile', function()
	{
		//
	}));

กำหนดให้ชื่อย่อนี้จะใช้ controller ไหน

	Route::get('user/profile', array('as' => 'profile', 'uses' => 'UserController@showProfile'));

ตอนนี้เราใช้ชื่อย่อ เพื่อสร้างลิ้งได้เเล้ว

	$url = URL::route('profile');

	$redirect = Redirect::route('profile');

เราใช้เมทอด`currentRouteName` เพื่อดึงชื่อของ Route ที่ทำงานในขณะนี้ได้

	$name = Route::currentRouteName();

<a name="route-groups"></a>
## Route Groups

เราสามารถกำหนดกลุ่มให้ Route ได้ทำให้สะดวกมากขึ้น

	Route::group(array('before' => 'auth'), function()
	{
		Route::get('/', function()
		{
			// Has Auth Filter
		});

		Route::get('user/profile', function()
		{
			// Has Auth Filter
		});
	});

<a name="sub-domain-routing"></a>
## Sub-Domain Routing

**การสร้าง Route ให้กับโดนเมนย่อย**

	Route::group(array('domain' => '{account}.myapp.com'), function()
	{

		Route::get('user/{id}', function($account, $id)
		{
			//
		});

	});
<a name="route-prefixing"></a>
## Route Prefixing

ในการกำหนดคำที่ใช้กำหนดกลุ่มของ เราใช้ prefix ในการตรวจสอบ

**ตัวอย่างการใช้ prefix**

	Route::group(array('prefix' => 'admin'), function()
	{

		Route::get('user', function()
		{
			//
		});

	});

<a name="route-model-binding"></a>
## Route Model Binding

คือการผูกโมเดลเขาไปกับ Route โดยใช้เมทอด `Route::model` 

**การใช้งาน**

	Route::model('user', 'User');

ต่อมาก็กำหนดให้เมื่อมีการเรียกลิ้งที่มี `{user}`เป็นพารามิเตอร์

	Route::get('profile/{user}', function(User $user)
	{
		//
	});

เราก็จะทำการแทรก `User` instance เข้าไปใน Route ยกตัวอย่าง `profile/1` ถูกเรียก `User` instance ก็จะมี ID = 1.



ถ้าพารามิเตอร์ที่ส่งเข้ามาไม่ตรงกับ `model` ใดๆเราสามารถกำหนดการแสดงข้อผิดพลาดำได้

	Route::model('user', 'User', function()
	{
		throw new NotFoundException;
	});

ต่อมา เมทอด `Route::bind` เป็นการผูกพารามิเตอร์เข้ากับ โมเดล เมื่อมีการส่งค่าเข้าตรงกับ route ที่กำหนดค่าก็จะก็จะถูกส่งมาที่เมทอดนี้ก่อน

	Route::bind('user', function($value, $route)
	{
		return User::where('name', $value)->first();
	});

