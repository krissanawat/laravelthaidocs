# Validation 

คือการตรวจสอบค่าต่างๆ ที่ป้อนเข้ามา หรือระหว่างการทำงานของฟังก์ชันต่างๆ โดยจะแสดงข้อผิดพลาดให้เราด้วย โดยคลาสที่ทำหน้าที่นั้นชื่อ Validator ครับ

<a name="basic-usage"></a>
## การใช้งานเบื้องต้น

**การใช้งานคลาส validator**

	$validator = Validator::make(
		array('name' => 'Dayle'),
		array('name' => 'required|min:5')
	);

อาเรย์ตัวแรกคือข้อมูลที่เราจะทำการตรวจนั้นเอง ตัวที่สองคือรูปแบบที่เราต้องการ
การใช้เครื่องหมาย | การตรวจสอบออกเป็นหลายๆ แบบ

**ใช้อาเรย์ในการกำหนดกฏ**

	$validator = Validator::make(
		array('name' => 'Dayle'),
		array('name' => array('required', 'min:5'))
	);

คลาส `Validator` จะสร้างเมทอด ชื่อ `fails` (หรือ `passes`) เพื่อตรวจสอบผล

	if ($validator->fails())
	{
		// The given data did not pass validation
	}

ถ้าไม่ผ่านเราสามารถดึงข้อความแสดงข้อผิดพลาดได้.

	$messages = $validator->messages();

เมทอด `failed` ใช้ในการเข้าถึงกฏที่เราตั้งไว้

	$failed = $validator->failed();


<a name="working-with-error-messages"></a>
## การจัดการข้อความแสดงข้อผิดพลาด

เมื่อเรียกเมทอด `messages` บนตัว `Validator` instance,เราจะได้รับ  `MessageBag` instance ที่จะมีเมทอดให้เราจัดการข้อความ

**แสดงข้อความแสดงข้อผิดพลาดเฉพาะตัวแรก**

	echo $messages->first('email');

**รับข้อความแสดงข้อผิดพลาดทั้งหมด**

	foreach ($messages->get('email') as $message)
	{
		//
	}

**รับข้อความแสดงข้อผิดพลาดจากทุคอลัมน์**

	foreach ($messages->all() as $message)
	{
		//
	}

**ตรวจว่ามีข้อความแสดงข้อผิดพลาดจากคอลัมน์ eamil ไหม**

	if ($messages->has('email'))
	{
		//
	}

**รับข้อความแสดงข้อผิดพลาดโดยใส่รูปแบบให้ด้วย**

	echo $messages->first('email', '<p>:message</p>');

> **หมายเหตุ:** โดยเริ่มต้น, รูปแบบข้อความจะถูกจัดในรูปแบบที่นำไปใช้งานร่วมกับ twiiter bootstrap ได้.

**รับข้อความแสดงข้อผิดพลาดทั้งหมดพร้อมใส่รูปแบบ**

	foreach ($messages->all('<li>:message</li>') as $message)
	{
		//
	}

<a name="error-messages-and-views"></a>
## การแสดงข้อความแสดงข้อผิดพลาดบน view

ตัวอย่างนี้เราจะส่งข้อความแสดงข้อผิดพลาด ไปให้ view 

	Route::get('register', function()
	{
		return View::make('user.register');
	});

	Route::post('register', function()
	{
		$rules = array(...);

		$validator = Validator::make(Input::all(), $rules);

		if ($validator->fails())
		{
			return Redirect::to('register')->withErrors($validator);
		}
	});

ถ้าการตรวจสอบไม่ผ่านเราจะใช้เมทอด `withErrors` ส่งข้อความแสดงข้อผิดพลาด
ขึ้นไปบน View ด้วย

 **ไม่ควรส่งข้อความแสดงข้อผิดพลาด ไปบน Route ทีเป็น method GET เพราะ laravel จะตรวจสอบข้อผิดพลาดบน session ทุกคำร้องขอ**

เมื่อทำการรีไดเรคเราสามารถเข้าถึงข้อความแสดงข้อผิดพลาด โดยใชตัวแปร`$errors` ดังตัวอย่างครับ

	<?php echo $errors->first('email'); ?>

<a name="available-validation-rules"></a>
## กฏในการตรวจสอบที่ laravel เตรียมไว้


<a name="rule-accepted"></a>
#### accepted

ค่าทีจะผ่านคือ _yes_, _on_, or _1_.เหมาะสำหรับใช้ในการตรวจสอบว่ายอมรับ "Terms of Service" ไหม

<a name="rule-active-url"></a>
#### active_url

ตรวจสอบว่าลิ้งตายยัง โดยใช้ `checkdnsrr` ซึ่งเป็น PHP function.

<a name="rule-after"></a>
#### after:_date_

ตรวจสอบว่าค่าที่ส่งมาเป็นมีรูปแบบของเวลาหลักจากใช้ `strtotime` อยู่หลังสุดแปลงไหม

<a name="rule-alpha"></a>
#### alpha

ตรวจสอบว่าค่าที่ส่งมาเป็นรูปแบบของตัวอักษรต่างๆ ไหม

<a name="rule-alpha-dash"></a>
#### alpha_dash

ตรวจสอบว่าค่าที่ส่งมาเป็นรูปแบบของตัวเลขที่มีเครื่องหมาย _ รวมอยู่ด้วยไหม

<a name="rule-alpha-num"></a>
#### alpha_num

ตรวจสอบว่าค่าที่ส่งมาเป็นรูปแบบของตัวเลขไหม

<a name="rule-before"></a>
#### before:_date_

ตรวจสอบว่าค่าที่ส่งมาเป็นมีรูปแบบของเวลาหลักจากใช้ `strtotime` อยู่หน้าสุดไหม


<a name="rule-between"></a>
#### between:_min_,_max_

ตรวจสอบว่าค่าที่ส่งมาเป็นมีค่าอยู่ระหว่าง min กับ max ไหม


<a name="rule-confirmed"></a>
#### confirmed

ตรวจสอบว่าค่าที่ส่งมาเป็นมีรูปแบบของฟอร์มที่มีรูปแบบ ชื่อ_confirmation ยกตัวอย่างการ
ตรวจสอบ `password`,ว่าตรงกับ`password_confirmation` ไหม

<a name="rule-date"></a>
#### date

ตรวจสอบว่าค่าที่ส่งมาเป็นมีรูปแบบของเวลาหลักจากใช้ `strtotime` ไหม.

<a name="rule-date-format"></a>
#### date_format:_format_

ตรวจสอบว่าค่าที่ส่งมาเป็นมีรูปแบบของเวลาที่กำหนดไหม

<a name="rule-different"></a>
#### different:_field_

ค่าตรง _field_ ต้องมีค่าต่างจากค่าที่ป้อนเข้ามาถึงจะผ่าน

<a name="rule-email"></a>
#### email

ตรวจสอบว่าค่าที่ส่งมาเป็นมีรูปแบบของ email

<a name="rule-exists"></a>
#### exists:_table_,_column_

ฟอร์มที่อยู่ในการตรวจสอบต้องมีชื่อตรงกับคอลัมน์ในฐานข้อมูล

**การใช้งานเบื้องต้น**

	'state' => 'exists:states'

**การใช้งานโดยใส่ค่าที่ต้องการตรวจไปหลายค่า**

	'state' => 'exists:states,abbreviation'

เราสามารถกำหนดเงื่อนไขให้กฏคล้ายๆการทำคิวรี้ ครับ

	'email' => 'exists:staff,email,account_id,1'

<a name="rule-image"></a>
#### image

ตรวจสอบว่าค่าที่ส่งมาเป็นรูปภาพมีนามสกุล(jpeg, png, bmp, or gif) ไหม

<a name="rule-in"></a>
#### in:_foo_,_bar_,...

ตรวจสอบว่าค่าที่ส่งมามีค่าตรงกับค่าใน foo,bar ไหม

<a name="rule-integer"></a>
#### integer

ตรวจสอบว่าค่าที่ส่งมามีรูปแบบของเลขจำนวนเต็มไหม

<a name="rule-ip"></a>
#### ip

ตรวจสอบว่าค่าที่ส่งมามีรูปแบบของ IP address.

<a name="rule-max"></a>
#### max:_value_

ตรวจสอบว่าค่าที่ส่งมามีค่าน้อยกว่าค่าที่กำหนดไว้

<a name="rule-mimes"></a>
#### mimes:_foo_,_bar_,...

ตรวจสอบว่าค่าที่ส่งมามีรูปแบบของ mime type ตรงกับที่กำหนดไหม

**ตัวอย่างการตรวจสอบนามสกุลของไฟล์**

	'photo' => 'mimes:jpeg,bmp,png'

<a name="rule-min"></a>
#### min:_value_

ตรวจสอบว่าค่าที่ส่งมามีจำนวนน้อยกว่าไหมถ้ามีน้อยกว่าก็ไม่ผ่าน

<a name="rule-not-in"></a>
#### not_in:_foo_,_bar_,...

ตรวจสอบว่าค่าที่ส่งมามีค่าตรงกับค่าที่ตั้งไว้ไหม ถ้ามีก็ไม่ผ่านครับ

<a name="rule-numeric"></a>
#### numeric

ตรวจสอบว่าค่าที่ส่งมาเป็นตัวเลขไหม

<a name="rule-regex"></a>
#### regex:_pattern_

ตรวจสอบว่าค่าที่ส่งมามีรูปแบบกับ regular expression ที่กำหนดไว้ไหม


<a name="rule-required"></a>
#### required

ตรวจสอบว่าค่าที่ส่งมาเป็นค่าว่างไหม ถ้าเป็นก็ไม่ผ่านครับ

<a name="rule-required-if"></a>
#### required_if:_field_,_value_

ตรวจว่าค่าใน _field_ ต้องไม่ว่างและ ตรงกับ _value_

<a name="rule-required-with"></a>
#### required_with:_foo_,_bar_,...

ตรวจว่าฟิล _foo_ ต้องมีค่าหาก bar มีค่าด้วย

<a name="rule-required-without"></a>
#### required_without:_foo_,_bar_,...

ตรวจว่าฟิล _foo_ ต้องมีค่าหาก bar ไม่มีค่า

<a name="rule-same"></a>
#### same:_field_

ตรวจว่าค่าที่ส่งเข้ามาซ้ำกับค่าที่กำหนดไว้ไหม

<a name="rule-size"></a>
#### size:_value_

ตรวจว่าค่าที่ส่งเข้ามาตรงกับที่กำหนดไว้ไหม กรณีเป็นคำจะตรวจสอบจำนวนคำ เป็นตัวเลขก็เทียบตามค่า เป็ไฟล์เทียบตามขนาดของไฟล์เป็นกิโลไบต์

<a name="rule-unique"></a>
#### unique:_table_,_column_,_except_,_idColumn_

ตรวจว่าค่าที่ส่งมาซ้ำกับในตารางไหม.

**ตัวอย่างการใช้ตรวจว่าอีเมล์นี้มีในตาราง user ไหม**

	'email' => 'unique:users'

**ตัวอย่างการใช้ตรวจว่าอีเมล์นี้มีในตาราง user ตรงคอลัมน์ email-address ไหม**

	'email' => 'unique:users,email_address'

ตัวอย่างการใช้ตรวจว่าอีเมล์นี้มีในตาราง user ตรงคอลัมน์ email-address ไหม โดยไม่สนใจ id ที่มีค่าเท่ากับ 10

	'email' => 'unique:users,email_address,10'

<a name="rule-url"></a>
#### url
ตรวจว่าค่าเป็น url ไหม

<a name="custom-error-messages"></a>
## Custom Error Messages

เราสามารถปรับแต่งข้อความที่แสดงข้อผิดพลาดได้

**ตัวอย่าง**

	$messages = array(
		'required' => 'The :attribute field is required.',
	);

	$validator = Validator::make($input, $rules, $messages);

**การใช้ข้อความที่เรากำหนดร่วมกับ Place-Holders**

	$messages = array(
		'same'    => 'The :attribute and :other must match.',
		'size'    => 'The :attribute must be exactly :size.',
		'between' => 'The :attribute must be between :min - :max.',
		'in'      => 'The :attribute must be one of the following types: :values',
	);

**กำหนดข้อความให้แต่ละคอลัมน์เลย**

	$messages = array(
		'email.required' => 'We need to know your e-mail address!',
	);

บางกรณีเราต้องการกำหนดข้อความที่แสดงให้เป็นเฉพาะแต่ละภาษาไป ซึ่งเราต้องไปเพิ่มที่อาเรย์ชื่อ `custom` ใน `app/lang/xx/validation.php` ตาม
ภาษาที่ไป

**คัวอย่าง**

	'custom' => array(
		'email' => array(
			'required' => 'We need to know your e-mail address!',
		),
	),


<a name="custom-validation-rules"></a>
## การสร้างตัวตรวจสอบ

เราสามารถสร้างฟังก์ชันในการตรวจสอบได้เองโดย laravel เตรียมเมทอด `Validator::extend` มาเพื่อการนั้นครับ

**ตัวอย่าง**

	Validator::extend('foo', function($attribute, $value, $parameters)
	{
		return $value == 'foo';
	});


ตัวอย่างข้างบนเรารับตัวแปรมาสามตัวครับ `$attribute` คือชื่อข้อมูลที่จะตรวจ  `$value` ค่าของข้อมูล  `$parameters` ค่าอื่นๆ

You may also pass a class and method to the `extend` method instead of a Closure:

	Validator::extend('foo', 'FooValidator@validate');

มีอีกวิธีในการสร้างคลาสของเราเองโดยการสืบทอด `Illuminate\Validation\Validator` ทีนี้ฟังก์ชันต้องมีคำว่า `validate`นำหน้าด้วยนะครับ

**ตัวอย่าง**

	<?php

	class CustomValidator extends Illuminate\Validation\Validator {

		public function validateFoo($attribute, $value, $parameters)
		{
			return $value == 'foo';
		}

	}

**ต่อมาเราต้องเอาคลาสของเรามาลงทะเบียน**

	Validator::resolver(function($translator, $data, $rules, $messages)
	{
		return new CustomValidator($translator, $data, $rules, $messages);
	});
เราสามารถสร้างเมทอดที่ใช้ในการแสดงข้อผิดพลาดโดยตามรูปแบบนี้ครับ `replaceXXX` ตามตัวอย่าง

	protected function replaceFoo($message, $attribute, $rule, $parameters)
	{
		return str_replace(':foo', $parameters[0], $message);
	}
