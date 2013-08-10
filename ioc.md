# IoC Container

<a name="introduction"></a>
## Introduction
>หมายเหตุ :: ถ้าใครยังไม่ค่อยเข้าใจว่าสองตัวนี้มันคืออะไรลองไปอ่านที่ผมสรุปไว้[ที่นี้](http://taqmaninw.com/inversion-of-control-กับ-dependency-injection-คืออะไร/)ก่อนครับ

 IOC  คือคลาสที่ใช้ในการจัดการ `library` ภายนอกที่เรานำเข้ามาใช้ หรือที่ดึงเข้ามาใช้โดย `composer `ต่อไปนี้จะเรียกย่อๆว่า IOC นะครับ
IOC จะช่วยในการเรียกใช้งานคลาสต่าง การทำความเข้าใจ IOC ถือว่าเป็นหัวใจ เลยในการปูทางสู่การทำเว็บขนาดใหญ่สำหรับ
laravel นะครับ เพราะหลักการนี้จะเกี่ยวโยงไปถึงเรื่อง `Service Provider` กับ `Facade`

<a name="basic-usage"></a>
## การใช้งานเบื้องต้น


**การใช้งาน IOC ในการผูก object**

	App::bind('foo', function($app)
	{
		return new FooBar; //
	});
 ง่ายคือต่อไปนี้พารามิเตอร์ชื่อ foo จะใช้เป็นตัวแทนของ class FooBar ที่ถูกสร้างเป็นวัตถุแแล้ว

**การเรียกใช้งาน**

	$value = App::make('foo');

เมื่อเราเรียกใช้งาน `App::make` เมทอด ฟังก์ชันข้างบนก็จะถูกเรียกใช้งาน ตัวแปร value
ก็จะรับคุณสมบัติต่างๆ ของคลาส FooBar เข้ามา

บางครั้งเราไม่ต้องการสร้าง `instane` ทุกครั้งที่รีเฟรช laravel มีฟังก์ชัน singleton มาให้ใช้ในการนี้เลยครับ

**ตัวอย่างการใช้งาน**

	App::singleton('foo', function()
	{
		return new FooBar;
	});

ถ้าไม่ต้องการจัดเพิ่มเข้าไปทั้งคลาสจะยัดเข้าไปเป็นออปเจ็คก็ใช้ฟังก์ชัน `instance` ได้เลยครับ

**ตัวอย่างการผูกออปเจคเข้าไป**

	$foo = new Foo;

	App::instance('foo', $foo);

<a name="automatic-resolution"></a>
## Automatic Resolution

ตัวอย่างการผูกคลาสอย่างรวดเร็วขึ้นโดยไม่ต้องใช้ `App::bind` แล้วแต่ขอให้ชื่อตรงกันก็พอ

**ตัวอย่าง**

	class FooBar {

		public function __construct(Google $baz)
		{
			$this->baz = $baz;
		}

	}

	$fooBar = App::make('FooBar');

ในตัวอย่างนี้ ตัวแปร $foofBar จะเก็บค่าคลาส `Google` ที่ถูกแทรกเข้ามา

laravel จะทำการเรียกใช้ [reflection class](http://taqmaninw.com/Reflection-class-คืออะไร) ของ php เพื่อทำการตรวจสอบค่าต่างๆ ในคลาสนั้นให้เองครับ  
บางกรณีคลาสที่เราจะใช้งานดันไปดึงคลาสที่เป็น interface เข้ามาใช้ด้วย เพื่อการนั้นเราต้องใช้ `App::bind` เมทอด ในการผูก ดังตัวอย่าง

**ตัวอย่างการผูก class ที่เป็นเรียกใช้งาน interface**

	App::bind('UserRepositoryInterface', 'DbUserRepository');

คลาสที่เราเรียกใช้งาน

	class UserController extends BaseController {

		public function __construct(UserRepositoryInterface $users)
		{
			$this->users = $users;
		}

	}

ตอนนี้ `UserRepositoryInterface` จะถูกเรียกใช้งานแล้ว
<a name="practical-usage"></a>
## การประยุกต์ใช้งาน

เพื่อความยืดหยุ่นในการทดสอบและใช้งาน Laravel เตรียมการให้เราใช้งาน IOC ไในหลายกรณีเลยครับ 

**ตัวอย่างการเรียกใช้งาน Class OrderRepository**

	class OrderController extends BaseController {

		public function __construct(OrderRepository $orders)
		{
			$this->orders = $orders;
		}

		public function getIndex()
		{
			$all = $this->orders->all();

			return View::make('orders', compact('all'));
		}

	}

ในตัวอย่างคลาส `OrderRepository` แทรกเข้าไปโดยอัติโนมัติเมื่อ คลาส `OrderController`  ทำงาน เมื่อมีการทำ [unit testing](/docs/testing) คลาส `OrderRepository` ก็จะถูกเพิ่มเข้ามาเหมือนกัน

[Filters](/docs/routing#route-filters), [composers](/docs/responses#view-composers), และ [event handlers](/docs/events#using-classes-as-listeners) อยู่นอกเหนือการทำงานของ IoC container เมื่อเราจะใช้ต้องทำตามตัวอย่างครับ

**การใช้งาน View::composer,Route::filter และ Event::listen กับ IOC**

	Route::filter('foo', 'FooFilter');

	View::composer('foo', 'FooComposer');

	Event::listen('foo', 'FooHandler');

<a name="service-providers"></a>
## Service Providers

Service providers เป็นการจับคลาส IOC ที่ทำงานคล้ายกันเข้ามาไว้ในที่เดียวกัน.
แล้วเรียกใช้งาน ด้วย facade 
โดยหลักเเล้วทุกคลาสหลักของ laravel ใช้การทำ service provider ในการจัดการเราสามารถเข้าไปดูได้ตรงที่ตัวแปรอาเรย์ `providers` ตรงที่`app/config/app.php` 

จะสร้าง Service Provider ขึ้นมาใช้กับคลาสของเราเริ่มแรกต้องดึงคลาส `Illuminate\Support\ServiceProvider` และประกาศเมทอด `register` 

**ตัวอย่างการสร้าง Service Provider**

	use Illuminate\Support\ServiceProvider;

	class FooServiceProvider extends ServiceProvider {

		public function register()
		{
			$this->app->bind('foo', function()
			{
				return new Foo;
			});
		}

	}

ในเมทอด `register` คลาส IOC จะเป็นตัวแปร `$this->app` ถ้าเราทำเสร็จแล้วก็ต้องเอาเส้นทางที่อยู่ของคลาส Provider ของเราไปเพิ่มในอาเรย์ `providers` ใน `app.php` ด้วย

การเรียกใช้งาน Service Provider ด้วยเมทอด `App::register`

**ตัวอย่าง**

	App::register('FooServiceProvider');

<a name="container-events"></a>
## Container Events

ใน IOC ก็มี event อยู่ชื่อเมทอดว่า `resolving` 

**การใช้งาน resoliving เพื่อรอดูว่ามี IOC ตัวไหนทำงานบ้าง**

	App::resolving(function($object)
	{
		//
	});

