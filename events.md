# Events

<a name="basic-usage"></a>
## การใช้งานเบื้องต้น

คลาส `Event` เตรียมมาให้เราใช้ในการดักจับเหตุการณ์ต่างๆที่เกิดขึ้นบน เว็บของเรา
หมายเหตุ Event คือเหตุการใดที่เกิดขึ้นบนเว็บเรา ยกตัวอย่างการเพิ่มลบแก้ไข , Listener คือ ฟังก์ชันที่คอยดักจับเหตุการ , fire คือฟังก์ชันที่สั่งให้เกิดเหตุการขึ้นเพื่อให้ listerner ทำงาน

**การดักรอฟังเหตุการณ์**

	Event::listen('user.login', function($user)
	{
		$user->last_login = new DateTime;

		$user->save();
	});

**การสั่งให้เกิดเหตุการณ์ขึ้น**

	$event = Event::fire('user.login', array($user));

เราสามารถกำหนดลำดับเหตุการที่จะให้เกิดได้ โดยค่าลำดับที่เกิดเรียงจากน้อยไปหามาก.

**การใช้ลำดับควบคุมการทำงาน**

	Event::listen('user.login', 'LoginHandler', 10);

	Event::listen('user.login', 'OtherHandler', 5);

บางครั้งเราอยากจะให้ฟังก์ชันที่ดักฟังอยู่ทำงานแค่ครั้งเดียว เราสามารถใช้การ return `false` เพื่อหยุดฟังก์ชันนี้ได้

**หยุดการทำงานของเหตุการ**

	Event::listen('user.login', function($event)
	{
		// Handle the event...

		return false;
	});

<a name="wildcard-listeners"></a>
## Wildcard Listeners

คือการดักฟังทุกเหตุการเลย ไม่เฉพาะเจาะจงเเล้ว

**การใช้งาน**

	Event::listen('foo.*', function($param, $event)
	{
		// Handle the event...
	});

ทีนี้ถ้ามีเหตุการณ์อะไรที่ขึ้นต้นด้วย `foo.`.ฟังก์ชันในตัวอย่างก็จะทำงาน

<a name="using-classes-as-listeners"></a>
## ใช้ Classes กับ Listeners

ในบางกรณี เราสามารถผูกคลาสเข้ากับเหตุการได้ โดย laravel จะใช้การทำ [Laravel IoC container](/docs/ioc),ในการจัดการ

**ตัวอย่าง**

	Event::listen('user.login', 'LoginHandler');

โดยค่าเริ่มต้นฟังก์ชัน `handle` ในคลาส `LoginHandler`จะถูกเรียกก่อนเลย เหมือนกับ _construct 

**ฟังก์ชัน handle จะถูกเรียกใช้เลย**

	class LoginHandler {

		public function handle($data)
		{
			//
		}

	}

ถ้าเราไม่ต้องการ ก็ผูกเมทอดที่เราต้องการเข้าไปดังนี้ครับ

	Event::listen('user.login', 'LoginHandler@onLogin');

<a name="queued-events"></a>
## การเรียงลำดับเหตุการ

ใช้ฟังก์ชัน `queue` กับ `flush` ในการเรียงลำดับการเกิดเหตุการ

**การสร้างลำดับ**

	Event::queue('foo', array($user));


<a name="event-subscribers"></a>
## Event Subscribers

คือคลาสที่เราแบ่งกลุ่มฟังก์ชันที่ใช้จัดการเหตุการ โดยต้นทางมาจากฟังก์ชัน `subscribe` 

**ตัวอย่างการสร้างคลาส Subscriber**

	class UserEventHandler {

		/**
		 * ติดตามการเข้าใช้งานของผู้ใช้งาน
		 */
		public function onUserLogin($event)
		{
			//
		}

		/**
		 * ติดตามการออกจากระบบ
		 */
		public function onUserLogout($event)
		{
			//
		}

		/**
		 * สร้างฟังก์ชันเพื่อใช้งานคลาส
		 *
		 * @param  Illuminate\Events\Dispatcher  $events
		 * @return array
		 */
		public function subscribe($events)
		{
			$events->listen('user.login', 'UserEventHandler@onUserLogin');

			$events->listen('user.logout', 'UserEventHandler@onUserLogout');
		}

	}

เมื่อประกาศคลาสแล้ว การนำไปใช้งานเราก็ต้องเอาไปลงทะเบียนกับคลาสหลัก

**ตัวอย่าง**

	$subscriber = new UserEventHandler;

	Event::subscribe($subscriber);
