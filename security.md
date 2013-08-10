# Security
คลาสนี้ใช้ในการสร้างระบบรักษาความปลอดภัยต่างอย่างเช่น การเข้ารหัสเพื่อใช้ใน password,session,cookie
<a name="configuration"></a>

<a name="storing-passwords"></a>
## การเก็บรหัสผ่าน

Class `Hash` ของ `laravel` ใช้ส่วนขยาย `Bcrypt` ของ php มาพัฒนาต่อยอด

**การสร้างค่า hash**

	$password = Hash::make('secret');

**การตรวจสอบค่า hash**

	if (Hash::check('secret', $hashedPassword))
	{
		// The passwords match...
	}

**ตรวจว่า password ต้องการเข้ารหัสอีกครั้ง กรณีลืมรหัสผ่าน**

	if (Hash::needsRehash($hashed))
	{
		$hashed = Hash::make('secret');
	}

<a name="authenticating-users"></a>
## การยืนยันตัวบุคคล

การล็อกอิน laravel เตรียมเมทอด `Auth::attempt` มาให้ตัวอย่างการใช้งาน

	if (Auth::attempt(array('email' => $email, 'password' => $password)))
	{
		return Redirect::intended('dashboard');
	}

ค่า `email` เราสามารถเปลี่ยนไปตามใจเราได้ครับ ส่วนเมทอด `Redirect::intended` ใช้ส่งผู้ใช้งานกลับไปที่ลิ้งที่เข้าเรียกมาครับ

เมื่อเมทอด `attempt` ถูกเรียก event `auth.attempt` จะถูกเรียกและ event `auth.login` จะถูกเรียกเมื่อการเข้าสู่ระบบสำเร็จ


**ตรวจสอบว่ามีการล็อกอินค้างอยู่ไหม**

	if (Auth::check())
	{
		// The user is logged in...
	}


**ตัวอย่างการปรับปรุงเมทอด attempt ให้สามารถทำการจำชื่อผู้ใช้กับรหัสผ่านได้**

	if (Auth::attempt(array('email' => $email, 'password' => $password), true))
	{
		// The user is being remembered...
	}



**กากำหนดเงื่อนไขตอนล็อกอิน**

    if (Auth::attempt(array('email' => $email, 'password' => $password, 'active' => 1)))
    {
        // ถ้า active เป็น 1 แสดงว่ายืนยันอีเมล์แล้ว
    }


**การเข้าถึงเข้อมูลคนที่ล็อกอิน**

	$email = Auth::user()->email;

ใช้เมทอด `loginUsingId` เพื่อดึง Id ของคนที่ล็อกอินมา

	Auth::loginUsingId(1);

สมมุตว่าเรากำหนดว่าการเปลี่ยนแปลงเลขบัตรเครดิตต้องใช้รหัสผ่านยืนยันเพิ่มเติม เมทอด `validate` สามารถทำงานนี้ให้เราได้

**การใช้งานการยืนยันตัวตนโดยไม่ไดเข้าสู่ระบบ**

	if (Auth::validate($credentials))
	{
		//
	}



**การล็อกอินแบบไม่มี session หรือ cookies**

	if (Auth::once($credentials))
	{
		//
	}

**เมทอดที่ใช้ล็อกเอาท์**

	Auth::logout();

<a name="manually"></a>
## การจำลองการล็อกอิน

การล็อกอินแบบ ที่เราจำลองขึ้้นมาเองครับ เมทอด`login` ใช้ค่าจากฐานข้อมูลมาล็อกอินได้ทันทีเลย

	$user = User::find(1);

	Auth::login($user);


### การป้องกัน CSRF 


**ทำการแนบค่า token เข้ากับฟอร์ม**

    <input type="hidden" name="_token" value="<?php echo csrf_token(); ?>">

**ตรวจสอบค่า token ที่ถูกส่งมา**

    Route::post('register', array('before' => 'csrf', function()
    {
        return 'You gave a valid CSRF token!';
    }));

<a name="http-basic-authentication"></a>
## HTTP Basic Authentication

laravel เตรียม filter ชื่อ `auth.basic` เพื่อตรวจว่า มีการล็อกอินไหม

**ตัวอย่าง**

	Route::get('profile', array('before' => 'auth.basic', function()
	{
		// Only authenticated users may enter...
	}));

โดยค่าเริ่มต้นเเล้วเมทอด `basic` ใช้คอลัมน์ email ในการตรวจสอบ ถ้าเราจะเปลี่ยนก็ใช้

	return Auth::basic('username');

laravel เตรียมฟังก์ชัน `Oncebasic` มาเพื่อการล็อกอินแบบไม่สร้าง session ไว้เหมาะกับการให้ ผู้ใช้งานใช้ในกรณีไปล็อกอินเครื่องที่ไม่ใช่ของตัวเอง

**ตัวอย่าง**

	Route::filter('basic.once', function()
	{
		return Auth::onceBasic();
	});

<a name="password-reminders-and-reset"></a>
## การจัดการการลืมรหัสผ่าน 
การลืมรหัสผ่านและการสร้างใหม่
### ส่งรหัสผ่านใหม่

laravel เตรียมการมาให้เราสามารถสร้างระบบการส่และเปลี่ยนรหัสผ่าน ให้เราโดยการให้ `User` model ทำการสืบทอด `Illuminate\Auth\Reminders\RemindableInterface`.

**การใช้งาน RemindableInterface**

	class User extends Eloquent implements RemindableInterface {

		public function getReminderEmail()
		{
			return $this->email;
		}

	}

ต่อมาเราก็ต้องสร้างตารางให้ระบบลืมรหัสผ่านก่อนโดย `php artisan auth:reminders` 

**สร้างตัว migration ของตารางลืมรหัส**

	php artisan auth:reminders

	php artisan migrate

การส่งรหัสใช้เมทอด `Password::remind` 

**ตัวอย่างการใช้งาน**

	Route::post('password/remind', function()
	{
		$credentials = array('email' => Input::get('email'));

		return Password::remind($credentials);
	});


> **หมายเหตุ:** เราต้องสร้าง view ที่ชื่อ `auth.reminder.email`เพื่อรับ email เองนะครับ

เราสามารถส่งข้อความเพิ่มเติมให้ผู้ใช้โดยส่งพารามิเตอร์ไป `$message` ไปในฟังก์ชัน `remind` 

	return Password::remind($credentials, function($message, $user)
	{
		$message->subject('Your Password Reminder');
	});

โดยค่าเริ่มต้นเเล้วเมทอด `remind` จะส่งกลับมาที่หน้าที่เรียกใช้ ถ้าเกิดข้อผิดพลาดขึ้น ตัวแปร `error`จะมีค่าขึ้นใน session  ส่วนเมื่อสำเร็จตัวแปร `success` ก็จะปรากฏขึ้นมาใน session แทน หน้าตาของหน้า `auth.reminder.email` ควรเป็นแบบนี้ครับ

	@if (Session::has('error'))
		{{ trans(Session::get('reason')) }}
	@elseif (Session::has('success'))
		An e-mail with the password reset has been sent.
	@endif

	<input type="text" name="email">
	<input type="submit" value="Send Reminder">

### การรีเซตรหัสผ่าน

การสร้าง route เพื่อรับการที่ผู้ใช้งานกดลิ้งทำการรีเซตรหัสผ่าน

	Route::get('password/reset/{token}', function($token)
	{
		return View::make('auth.reset')->with('token', $token);
	});

หน้า view ที่ทำการให้ผู้ใช้งานทำการเปลี่ยนรหัสผ่าน

	@if (Session::has('error'))
		{{ trans(Session::get('reason')) }}
	@endif

	<input type="hidden" name="token" value="{{ $token }}">
	<input type="text" name="email">
	<input type="password" name="password">
	<input type="password" name="password_confirmation">

ตัวอย่างการสร้าง route เพื่อทำการรับค่ารหัสผ่านใหม่

	Route::post('password/reset/{token}', function()
	{
		$credentials = array('email' => Input::get('email'));

		return Password::reset($credentials, function($user, $password)
		{
			$user->password = Hash::make($password);

			$user->save();

			return Redirect::to('home');
		});
	});

ถ้าการเปลี่ยนรหัสผ่านสำเร็จ `User` instance และรหัสผ่านใหม่จะถูกเก็บลงฐานข้อมูลและ ส่งกลับไปหน้า `home`

<a name="encryption"></a>
## Encryption

Laravel เตรียมการเข้ารหัสแบบ AES-256 โดยส่วนเสริม mcrypt ของ PHP มาให้เเล้ว

**กาเข้ารหัส**

	$encrypted = Crypt::encrypt('secret');

> **Note:** มั่นใจว่าเราเปลี่ยนค่า  `key` ตรงที่ `app/config/app.php` ไม่งั้นการเข้ารหัสจะไม่ค่อยปลอดภัยครับ

**การถอดรหัส**

	$decrypted = Crypt::decrypt($encryptedValue);



**การกำหนดรูปแบบต่างๆ**

	Crypt::setMode('ctr');

	Crypt::setCipher($cipher);
