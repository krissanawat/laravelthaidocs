# Requests & Input
คลาสนี้ใช้จัดการคำร้องขอต่างๆ เช่น ค่าที่ส่งมาจากฟอร์ม
<a name="basic-input"></a>
## Basic Input


**รับเฉพาะค่าที่มีชื่อตรงกับที่กำหนด**

	$name = Input::get('name');

**กำหนดค่าสำรองกรณีค่าที่ส่งมาเป็น null**

	$name = Input::get('name', 'Sally');

**ตรวจว่าค่าที่ส่งมาเป็นค่าว่างไหม**

	if (Input::has('name'))
	{
		//
	}

**รับค่าทั้งหมดของคำร้องขอ**

	$input = Input::all();

**รับค่าเป็นกรณีๆ ไป**

	$input = Input::only('username', 'password');

	$input = Input::except('credit_card');



<a name="cookies"></a>
## Cookies

**การดึงค่าจาก cookies**

	$value = Cookie::get('name');

**สร้าง cookie และส่งคืนไปให้ผู้ใช้งาน**

	$response = Response::make('Hello World');

	$response->withCookie(Cookie::make('name', 'value', $minutes));

**สร้าง cookie ที่ไม่หมดอายุ**

	$cookie = Cookie::forever('name', 'value');

<a name="old-input"></a>
## Old Input

คือการที่เราเก็บค่าที่ได้จากฟอร์มไว เพื่อทำการใส่ในฟอร์มถัดไป หรือนำไปแสดงหลังจากหน้าโหลดเสร็จ

**การนำค่าในฟอร์มเก็บใส่ session**

	Input::flash();

**การรับค่าเฉพาะกรณี**

	Input::flashOnly('username', 'email');

	Input::flashExcept('password');

เราทำการส่งค่าในฟอร์มเก่าที่เก็บไว้ขึ้นไปในฟอร์ม

	return Redirect::to('form')->withInput();

	return Redirect::to('form')->withInput(Input::except('password'));


**การดึงค่าจากในฟอร์มเก่า**

	Input::old('username');

<a name="files"></a>
## Files

**รับค่าจากการอัพโหลดไฟล์**

	$file = Input::file('photo');

**ตรวจว่าไฟล์ถูกอัพโหลดไหม**

	if (Input::hasFile('photo'))
	{
		//
	}

ค่าที่ถูกคืนมาจะเป็นเมทอด `file` method ซึ่งมาจากคลาส `Symfony\Component\HttpFoundation\File\UploadedFile` ซึ่งสืบทอดมาจากคลาส `SplFileInfo` ซึ่งเตรียมเมทอดไว้ให้เราจัดการไฟล์ไว้เยอะเลยครับ

**เปลี่ยนที่อยู่ให้ไฟล์ที่อัพขึ้นมา**

	Input::file('photo')->move($destinationPath);

	Input::file('photo')->move($destinationPath, $fileName);

**ดึงค่าเส้นทางทีอยูของไฟล์่**

	$path = Input::file('photo')->getRealPath();

**ดึงชื่อเริ่มต้นของไฟล์**

	$name = Input::file('photo')->getClientOriginalName();

**ดึงค่าขนาดของไฟล์**

	$size = Input::file('photo')->getSize();

**ดึงนามสกุลของไฟล์**

	$mime = Input::file('photo')->getMimeType();

<a name="request-information"></a>
## Request Information

คลาส `Request`ของ laravel สืบทอดมาจากคลาส `Symfony\Component\HttpFoundation\Request` ตอไปนี้คือฟังก์ชันสำคัญครับ

**ดึงค่า URI จากการเรียกครั้งล่าสุด**

	$uri = Request::path();

**ตรวจสอบว่าคำร้องขอที่ส่งเข้ามาตรงกับกฏที่เราตั้งไว้ไหม**

	if (Request::is('admin/*'))
	{
		//
	}

**ดึงค่า url ของคำร้องขอ**

	$url = Request::url();

**ดึงค่า URI เฉพาะส่วน**

	$segment = Request::segment(1); 
    // http::/taqmaninw.com/admin/post/id?=3
    // $sengment จะเท่ากับ admin

**ดึงค่า header **

	$value = Request::header('Content-Type');

**ดึงค่าจากตัวแปร $_SERVER**

	$value = Request::server('PATH_INFO');

**ตรวจว่าคำขอเป็น ajax ไหม**

	if (Request::ajax())
	{
		//
	}

**ตรวจว่าคำขอมาจาก https ไหม**

	if (Request::secure())
	{
		//
	}
