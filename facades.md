# Facades
> **ความเห็นส่วนตัว** บทนี้จะต้องใช้จินตนาการเยอะหน่อยนะครับ เพราะเป็นเรื่องของแนวคิดที่ใช้สร้างจุดขายของตัว laravel เลยครับ ในการตัด $this-> ทิ้งไปทำให้โค้ดอ่านง่าย ทำความเข้าใจง่าย ลดบริมาณการเขียนลง ใครที่อยากจะเอาคลาสบน packagist มาทำเป็น package ใช้เองต้องอ่านครับ
<a name="introduction"></a>
## รู้จักด้านหน้าของตึกกันก่อน

>ถ้ายังไม่รู้จักว่ามันคืออะไรลองไปอ่านที่ผม[สรุป](http://taqmaninw.com/facade-design-pattern-เพิ่งรู้ว่าหน้าตึกมันเป็นแบบนี้)ไว้ก็ได้ครับ

Facades แปลเป็นไทยคือ ด้านหน้าของตึก ที่มาของมันคือการใช้ facade design pattern เข้ามาจัดการทำ ให้คลาสที่เราเรียกใช้งานกลายเป็นแบบ static แทนที่จะสร้างเป็นออปเจคเหมือนที่ผ่านมา การทำแบบ static คือการเรียกใช้คลาสนั้นตรงๆ เลยครับ การทำแบบนี้จะทำให้รูปแบบของฟังก์ชันดูเข้าใจได้ง่ายมาก 
บางครั้งเราไปเจอคลาสที่เจ๋งๆ และอยากเอาเข้ามาใช้ใน laravel เราก็อยากให้มันเรียกแบบ static ได้เพราะฉะนั้นเราจึงต้องมาดูบททนี้ครับ

> **Note:** ก่อนที่จะมาเจาะลึกลงไปในโครงสร้างหลักของ laravel แนะนำให้ไปอ่านบบทนี้ [IoC container](/docs/ioc) ก่อนครับ

<a name="explanation"></a>
## หลักการเบื้องต้น

โดยโครงสร้างหลักของ laravel แล้ว, facade คลาสถูกวางให้ใช้ในการเรียกใช้งาน วัตถุที่ถูกลงทะเบียนไว้ในคลาส Container ในการสร้าง facade ขึ้นใช้เองนั้น เราต้องการแค่เมทอด `getFacadeAccessor`แค่ตัวเดียวครับ ตัวคลาส `Facade` หลักจะทำการใช้ `__callStatic()` ซึ่งเป็น magic-method ของ php จะไปทำการเรียกออปเจ็คของคลาสที่เราทำการลงทะเบียนไว้ที่คลาส  `Service Provider`
ของ ไลบราลี่ นั้นๆ 

<a name="practical-usage"></a>
## การประยุกต์ใช้งาน

ในตัวอย่าง, เรายกตัวอย่าง คลาส `Cache`. ซึ่งเรียกตัวฟังก์ชัน `get` ซึ่งตอนนี้เป็นรูปแบบ static นะครับ

	$value = Cache::get('key');

แต่ถ้าเราตามเข้าไปดูตามเส้นทางนี้ `Illuminate\Support\Facades\Cache` เราจะไม่เห็นฟังก์ชัน `get` อยู่เลย

	class Cache extends Facade {

		/**
		 * Get the registered name of the component.
		 *
		 * @return string
		 */
		protected static function getFacadeAccessor() { return 'cache'; }

	}

การทำงานคือเมทอด `getFacadeAccessor()`จะทำการส่งค่าที่เราทำการผูกไว้ในคลาสหลัก โดยใช้สตริงที่เรากำหนดตรง return ในการค้นหาคลาสที่ตรงกันให้

เมื่อผู้ใช้งานอ้างอิงถึงตัว `Cache` คลาสในรูปแบบ static, Laravel จะผูก`cache` เข้ากับ IoC container และส่งคำขอฟังก์ชันตามในตัวอย่าง (กรณีนี้เป็น `get`) 

ถ้าจะเขียน `Cache::get` แบบปกติจะได้แบบนี้ครับ

	$value = $app->make('cache')->get('key');

<a name="creating-facades"></a>
## การสร้าง Facades

การสร้าง Facade ให้ package หรือ คลาสภายนอกต้องการ 3 อย่างครับ

- การทำ IoC bind
- คลาส facade
- การสร้าง alias ให้ facade

ตัวอย่างการสร้าง package แบบง่ายครับ
`PaymentGateway\Payment`.

	namespace PaymentGateway;

	class Payment {

		public function process()
		{
			//
		}

	}

เราต้องทำการผูกคลาสเข้ากับตัวคลาสหลักก่อน

	App::bind('payment', function()
	{
		return new \PaymentGateway\Payment;
	});

ที่ๆ เราจะนำฟังก์ชันข้างบนไปเขียนไว้คือที่ไฟล์ [service provider](/docs/ioc#service-providers) ที่ชื่อ `PaymentServiceProvider` โดยใส่ไว้ข้างในเมทอด `register`เราต้องเอาเส้นทางที่อยู่ของคลาส `PaymentServiceProvider` ไปใส่ตรงที่ `app/config/app.php` ด้วย

ต่อมา เราก็สร้างคลาส Facade ให้กับคลาส Payment

	use Illuminate\Support\Facades\Facade;

	class Payment extends Facade {

		protected static function getFacadeAccessor() { return 'payment'; }

	}

สุดท้าย เรานำที่อยู่ของไฟล์ Facade ไปใส่ที่อาเรย์ชื่อ `aliases` ใน `app/config/app.php` และต้องใช้คำสั่ง `php artisan dump-autoload ` ก่อนนะครับไม่งั้น laravel จะไม่เจอคลาส แล้วสุดท้ายเราสามารถเรียกเมทอด `process` ของ คลาส `Payment` ในรูปแบบ static ได้เเล้ว

	Payment::process();


