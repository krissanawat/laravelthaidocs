# Forms & HTML

เป็นคลาสที่ใช้จัดการ Form กับ html

<a name="opening-a-form"></a>
## การเปิดฟอร์ม

**ตัวอย่าง**

	{{ Form::open(array('url' => 'foo/bar')) }}
		//
	{{ Form::close() }}

โดยค่าเริ่มต้นชนิดของคำร้องขอจะเป็น `POST` ถ้าจะเปลี่ยนก็เเค่ใส่พารามิเตอร์ไปเหมือนในตัวอย่างครับ

	echo Form::open(array('url' => 'foo/bar', 'method' => 'put'))


สามารถกำหนดเป้าหมายของไฟล์ที่จะส่งค่าไปได้หลายรูปแบบตามตัวอย่างเลยครับ

	echo Form::open(array('route' => 'route.name'))

	echo Form::open(array('action' => 'Controller@method'))

จะกำหนดพารามิเตอร์โดยเฉพาะเลยก็ตามตัวอย่างครับ:

	echo Form::open(array('route' => array('route.name', $user->id)))

	echo Form::open(array('action' => array('Controller@method', $user->id)))

ถ้าจะสร้างฟอร์มมาอัพโหลดไฟล์ก็ต้องตั้งค่าแบบตัวอย่างครับ `files` 

	echo Form::open(array('url' => 'foo/bar', 'files' => true))

<a name="csrf-protection"></a>
## การป้องกัน CSRF 

Laravel เตรียมการป้องกันโดยสร้างค่า hash ขึ้นจาก session ของ user แล้วสร้าง hidden form ขึ้นมาใส่ไว้ เราเพียงแต่ใช้เมทอด `token`ประกาศไว้ก็เสร็จเเล้วครับ

**ตัวอย่าง**

	echo Form::token();

**การป้องกัน csrf จากใน route**

	Route::post('profile', array('before' => 'csrf', function()
	{
		//
	}));

<a name="form-model-binding"></a>
## การดึงค่าจากตารางมาใส่ในฟอร์ม

laravel เตรียมเมทอด `Form::model` มาเพื่อการนั้นครับ

**ตัวอย่าง**

	echo Form::model($user, array('route' => array('user.update', $user->id)))

ถ้าชื่อของคอลัมน์ตรงกับ ชื่อของฟอร์มค่าก็จะปรากฏมาโดยอัติโนมัติ อย่างเช่นฟอร์มชื่อ `email`,ตรงกับโมเดลชื่อ `email` แต่ค่าที่จะปรากฏบนฟอร์มไม่ได้มีแค่ค่าที่มาจากโมเดลอย่างเดียว มีจาก session ค่าที่มาจากการส่งพารามิเตอร์อีก ลำดับการแสดงค่าจึงตามข้างล่างนี้ครับ 
1. ค่าจาก Session (ค่าเก่าที่เกิดจาการป้อน)
2. ค่าจากการส่งพารามิเตอรื
3. ค่าจากโมเดล

ซึ่งเมื่อ server ส่งค่าการตรวจสอบค่าที่ป้อนมาว่าผิดพลาด ค่าที่ส่งมาจากโมเดลก็จะตามกลับขึ้นมาด้วย

> **หมายเหตุ:** เมือใช้ `Form::model`อย่าลืม `Form::close`!

<a name="labels"></a>
## การใส่ป้ายชื่อ

**การใส่ป้ายชื่อให้ฟอร์ม**

	echo Form::label('email', 'E-Mail Address');

**การใส่คลาสให้ฟอร์ม**

	echo Form::label('email', 'E-Mail Address', array('class' => 'awesome'));

> **หมายเหตุ:** หลังจากใส่ค่า label ชื่อฟอร์ม ค่า id ก็จะตั้งตามค่า label โดยอัติโนมัติ.

<a name="text"></a>
## Text, Text Area, Password & Hidden Fields


**ตัวอย่างฟอร์มที่ใช้รับค่า**

	echo Form::text('username');

**กำหนดค่าเริ่มต้นให้ฟอร์ม**

	echo Form::text('email', 'example@gmail.com');

> **หมายเหตุ:** *hidden* และ *textarea* ฟังก์ชันใช้งานเหมือน *text* เมทอด

**สร้างฟอร์มรับรหัสผ่าน**

	echo Form::password('password');
	
**สร้างฟอร์มชนิดอื่น**

	echo Form::email($name, $value = null, $attributes = array());
	echo Form::file($name, $attributes = array());
	
<a name="checkboxes-and-radio-buttons"></a>
## Checkboxes and Radio Buttons

**สร้างฟอร์มชนิดเลือกค่า**

	echo Form::checkbox('name', 'value');
	
	echo Form::radio('name', 'value');

**สร้างฟอร์มชนิดเลือกค่าโดยค่าเริ่มต้นคือเลือกไว้เเล้ว**

	echo Form::checkbox('name', 'value', true);
	
	echo Form::radio('name', 'value', true);

<a name="file-input"></a>
## File Input

**สร้างฟอร์มอัพโหลดไฟล์**

	echo Form::file('image');

<a name="drop-down-lists"></a>
## Drop-Down Lists

**สร้างฟอร์มให้เลือกค่าแบบดรอบดาวน์**

	echo Form::select('size', array('L' => 'Large', 'S' => 'Small'));

**สร้างฟอร์มชนิดเลือกค่าแบบดรอบดาวน์โดยค่าเริ่มต้นคือเลือกไว้เเล้ว**

	echo Form::select('size', array('L' => 'Large', 'S' => 'Small'), 'S');

**แบ่งกลุ่มให้ตัวเลือก**

	echo Form::select('animal', array(
		'Cats' => array('leopard' => 'Leopard'),
		'Dogs' => array('spaniel' => 'Spaniel'),
	));

<a name="buttons"></a>
## Buttons

**สร้างปุ่มส่งค่า**

	echo Form::submit('Click Me!');

> **หมายเหตุ:** ถ้าจะสร้างปุ่มธรรมดาก็ใช้ button

<a name="custom-macros"></a>
## Custom Macros

macro คือชุดของ html ที่เราเขียนเตรียมไว้ สามารถนำเอาไปแทรกตามใจเราได้

**การสร้าง Form Macro**

	Form::macro('myField', function()
	{
		return '<input type="awesome">';
	});


**เรียก Form Macro มาใช้**

	echo Form::myField();
