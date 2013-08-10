# Controllers


<a name="basic-controllers"></a>
## การทำงานเบื้องต้น

แทนที่เราจะวางฟังก์ชันไว้ที่ไฟล์ `routes.php` ควรเขียนลงไปที่ controller ดีกว่าครับ จะทำให้โค้ดมีระเบียบเรียบร้อย อ่านง่าย

เราจะสร้าง Controllers ไว้ที่โฟลเดอร์  `app/controllers` โฟลเดอร์นี้จะถูกกำหนดที่อยู่ไว้ในตัวแปร `classmap` ในไฟล์ `composer.json` โดยเริ่มต้น .

ตัวอย่างการเรียกใช้ controller:

	class UserController extends BaseController {

		/**
		 *
		 */
		public function showProfile($id)
		{
			$user = User::find($id);

			return View::make('user.profile', array('user' => $user));
		}

	}

ทุก controller จะสืบทอดคลาส `BaseController` จะเก็บไว้ที่โฟลเดอร์ `app/controllers`โดยคลาส `BaseController` ก็สืบทอดต่อมาจาก `Controller` ต่อไปคือตัวอย่างการลงทะเบียน controller ไว้ที่ route ครับ

	Route::get('user/{id}', 'UserController@showProfile');

ตัวอย่างนี้เป็นการเรียกใช้งาน controller แบบใช้ namespace นะครับ

	Route::get('foo', 'Namespace\FooController@method');

เราสามารถตั้งชื่อย่อให้เพื่อสะดวกต่อการใช้งาน ตามตัวอย่างข้างล่างเลยครับ

	Route::get('foo', array('uses' => 'FooController@method','as' => 'name'));

จะสร้างลิ้งไปที่ controller ก็ใช้ฟังก์ชัน `URL::action` 

	$url = URL::action('FooController@method');

ใช้ฟังก์ชัน `currentRouteAction` เพื่อเรียกคำร้องขอที่ผ่านมา

	$action = Route::currentRouteAction();

<a name="controller-filters"></a>
## Controller Filters

คือการกรองค่า ที่ส่งเข้ามายังตัวเว็บซึ่ง laravel ก็มีรูปแแบบการทำ filter ไว้ให้อยู่แล้วเหมือนในตัวอย่างคือ เมื่อเว็บเริ่มทำงาน ก็ตรวจว่าได้ลงชื่อเข้าใช้งานไหม  แล้วค่าที่ส่งมาปลอดภัยไหม ส่งเครื่องหมายแปลกประหลาดมาไหม โดยทำการตรวจด้วย filter ชื่อ csrf แล้วถ้ามีการเรียก fooAction กับ barAction ก็ทำการเก็บ log ไว้ด้วย

	class UserController extends BaseController {

		/**
		 * ฟังก์ชัน _construct จะถูกเรียกก่อนเมื่อมีการเรียกใช้คลาสนี้
		 */
		public function __construct()
		{
			$this->beforeFilter('auth');

			$this->beforeFilter('csrf', array('on' => 'post'));

			$this->afterFilter('log', array('only' =>
								array('fooAction', 'barAction')));
		}

	}

เราสามารถจับกลุ่มให้ตัวกรองได้ เหมือนในตัวอย่างนะครับ 

	class UserController extends BaseController {

		/**
		 * Instantiate a new UserController instance.
		 */
		public function __construct()
		{
			$this->beforeFilter(function()
			{
				//จะกรองค่าไหนบ้างก็ใส่ตรงนี้เลย
			});
		}

	}

<a name="restful-controllers"></a>
## RESTful Controllers

restful controller ของ laravel คือการกำหนดว่าเมื่อมีการส่งคำขอแบบนี้ไปที่ฟังก์ชันนี้ให้ตอบสนองแบบไหนนะครับ เป็นการกรองตามชนิดของคำร้อง ทั้ง `get,post,put,delete`  การกรองระดับชนิดของคำร้องขอ ทำให้เราจัดการได้ง่ายขึ้น

**การกำหนดค่า restful controller ใน route**

	Route::controller('users', 'UserController');

เมื่อเรากำหนด ดังตัวอย่างด้านบนแล้ว คลาส UserController ก็จะรับแค่ค่าที่เรากำหนด
โดยเราจะใส่ชนิดของคำร้องขอที่เราจะรับไว้ที่หน้าตัวฟังก์ชัน

	class UserController extends BaseController {

		public function getIndex()
		{
			// ฟังก์ชันนี้จะรับคำร้องขอที่เป็นชนิด get เท่านั้น
		}

		public function postProfile()
		{
			// ฟังก์ชันนี้จะรับคำร้องขอที่เป็นชนิด post เท่านั้น
		}

	}

ในกรณีที่ชื่อฟังชันเรามีหลายคำใช้เครื่องหมาย - เพื่อเรียกได้ดังตัวอย่างเราเรียกใช้ฟังก์ชัน adminprofile ซึงมีสองคำ
          `users/admin-profile` 

	public function getAdminProfile() {}

<a name="resource-controllers"></a>
## Resource Controllers

Resource controllers คือการลงทะเบียน restful controller ของเรากับ Route มีคำสั่งใน command line ที่ช่วยให้เราสร้าง restful controller ได้รวดร็วขึ้น. ตัวอย่าง เราอยากจะสร้าง controller เพื่อจัดการภาพเราก็ใช้คำสั่ง `controller:make` ตามตัวอย่าง

	php artisan controller:make PhotoController

แล้วก็ลงทะเบียนบอก route ว่า controller นี้เป็น restful 

	Route::resource('photo', 'PhotoController');

การใช้คำสั่งอัติโนมัติสร้างจะทำให้เราได้ restful controller แบบเต็มรูปแบบ ยังเป็นการสร้างตัวอย่างการใช้งาน restful ให้ด้วย

**รูปแบบของ restful controller ที่คำสั่ง artisan จะสร้างให้**

Verb      | Path                  | Action   | Route Name
----------|-----------------------|----------|-------------
GET       | /resource             | index    | resource.index
GET       | /resource/create      | create   | resource.create
POST      | /resource             | store    | resource.store
GET       | /resource/{id}        | show     | resource.show
GET       | /resource/{id}/edit   | edit     | resource.edit
PUT/PATCH | /resource/{id}        | update   | resource.update
DELETE    | /resource/{id}        | destroy  | resource.destroy

ถ้าเราไม่ต้องการสร้างทั้งหมดตามในตารางก็กำหนดเป็นรายตัวไปเลยครับ ดังตัวอย่าง

	php artisan controller:make PhotoController --only=index,show

	php artisan controller:make PhotoController --except=index

แล้วก็กำหนดค่าใน route ให้ใช้งานได้เฉพาะฟังก์ชันก็ได้

	Route::resource('photo', 'PhotoController',
					array('only' => array('index', 'show')));


<a name="handling-missing-methods"></a>
## การแสดงผลเมื่อมีคำร้องที่ไม่ถูกต้อง

เราจะใช้ฟังก์ชัน `missingMethod`เพื่อจัดการคำร้องขอที่ไม่ตรงตามที่เรากำหนดไว้ จะส่งไปไหนจะแสดงอะไรก็กำหนดได้เลยครับ:

**ตัวอย่าง**

	public function missingMethod($parameters)
	{
		//
	}