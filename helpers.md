# Helper Functions

เป็นคลาสอเนกประสงค์ที่รวมการจัดการอาเรย์,สตริง,ยูอาร์แอล อื่นๆ

<a name="arrays"></a>
## Arrays

### array_add

ฟังก์ชัน `array_add` ใช้เพิ่ม key / value ลงในอาเรย์ถ้าไม่มี key นั้นอยู่

	$array = array('foo' => 'bar');

	$array = array_add($array, 'key', 'value');

### array_divide

ฟังก์ชัน `array_divide` จะทำการแบ่งอาเรย์ที่ส่งเข้าไปออก เป็นสองก้อน ก้อนหนึ่งเป็น key ก้อนหนึ่งเป็น value

	$array = array('foo' => 'bar');

	list($keys, $values) = array_divide($array);

### array_dot

ฟังก์ชัน `array_dot` จะทำการแผ่อาเรย์หลายมิติ ออกเป็นมิติเดียว โดยเราจะใช้เครื่องหมายดอทในการเข้าถึงหลังจากใช้ฟังก์ชันแแล้ว

	$array = array('foo' => array('bar' => 'baz'));

	$array = array_dot($array);

	// array('foo.bar' => 'baz');

### array_except

ฟังก์ชัน `array_except` ใช้ลบค่า key หรือ value ออกจากอาเรย์

	$array = array_except($array, array('keys', 'to', 'remove'));

### array_fetch

ฟังก์ชัน  `array_fetch` จะส่งอาเรย์มิติเดียวที่เรากำหนดได้ว่าจะเอาค่าเฉพาะ คีย์ชื่ออะไร

	$array = array(array('name' => 'Taylor'), array('name' => 'Dayle'));

	var_dump(array_fetch($array, 'name'));

	// array('Taylor', 'Dayle');

### array_first

ฟังก์ชัน `array_first` จะส่งค่าแรกของอาเรย์คืนมา

	$array = array(100, 200, 300);

	$value = array_first($array, function($key, $value)
	{
		return $value >= 150;
	});


### array_flatten

ฟังก์ชัน `array_flatten` ทำการแตกอาเรย์หลายมิติลงมาเหลือมิติเดียว

	$array = array('name' => 'Joe', 'languages' => array('PHP', 'Ruby'));

	$array = array_flatten($array);

	// array('Joe', 'PHP', 'Ruby');

### array_forget

ฟังก์ชัน `array_forget` ใช้ลบค่าออกจากอาเรย์โดยกำหนดตำแหน่ง ด้วยใช้เครื่องหมายดอท
	$array = array('names' => array('joe' => array('programmer')));

	$array = array_forget($array, 'names.joe');

### array_get

ฟังก์ชัน `array_get` ใช้ดึงค่าออกจากอาเรย์โดยกำหนดตำแหน่ง ด้วยเครื่องหมายดอท

	$array = array('names' => array('joe' => array('programmer')));

	$value = array_get($array, 'names.joe');

### array_only

ฟังก์ชัน `array_only` ใช้ดึงค่าจากเฉพาะ key ที่เรากำหนด ได้ค่าเดียว

	$array = array('name' => 'Joe', 'age' => 27, 'votes' => 1);

	$array = array_only($array, array('name', 'votes'));

### array_pluck

ฟังก์ชัน `array_pluck` ใช้ดึงค่าตามคีย์ที่กำหนด ได้หลายๆค่า

	$array = array(array('name' => 'Taylor'), array('name' => 'Dayle'));

	$array = array_pluck($array, 'name');

	// array('Taylor', 'Dayle');

### array_pull

ฟังก์ชัน `array_pull` ใช้ดึงค่าออกมาพร้อมกับลบไปด้วย.

	$array = array('name' => 'Taylor', 'age' => 27);

	$name = array_pull($array, 'name');

### array_set

ฟังก์ชัน `array_set` ใช้เพิ่มค่าลงอาเรย์โดยกำหนดที่อยู่โดยใช้เครื่องหมายดอท

	$array = array('names' => array('programmer' => 'Joe'));

	array_set($array, 'names.editor', 'Taylor');

### array_sort

ฟังก์ชัน `array_sort` ใช้เรียงลำดับค่าในอาเรย์

	$array = array(
		array('name' => 'Jill'),
		array('name' => 'Barry'),
	);

	$array = array_values(array_sort($array, function($value)
	{
		return $value['name'];
	}));

### head

ใช้คืนค่าแรกของ อาเรย์ มีประโยช์มากในการทำการเรียกฟังก์ชันแบบต่อเนื่อง

	$first = head($this->returnsArray('foo'));

### last

ใช้คืนค่าสุดท้ายของ อาเรย์ มีประโยช์มากในการทำการเรียกฟังก์ชันแบบต่อเนื่อง

	$last = last($this->returnsArray('foo'));

<a name="paths"></a>
## Paths
ตัวแปรที่ใช้เก็บค่าที่อยู่ของโฟลเดอร์
### app_path

เก็บค่าที่อยู่ของโฟลเดอร์ `application` 

### base_path

เก็บค่าที่อยู่ของเว็บระดับ root เลย

### public_path

เก็บค่าที่อยู่ของโฟลเดอร์ `public` 

### storage_path

เก็บค่าที่อยู่ของโฟลเดอร์ `application/storage`

<a name="strings"></a>
## Strings
คลาสนี้ ใช้จัดการตัวอักษร เช่นแปลงเป็นตัวใหญ่ รูปแบบไวยกรณ์
### camel_case

แปลงคำให้ขึ้นต้นด้วยตัวใหญซึ่งเรียกว่า  `camelCase`.

	$camel = camel_case('foo_bar');

	// fooBar

### class_basename

ใช้ดึงชื่อคลาสจาก namespace path.

	$class = class_basename('Foo\Bar\Baz');

	// Baz

### e

เรียกใช้ฟังก์ชัน `htmlentites` เพื่อกรองค่า

	$entities = e('<html>foo</html>');

### ends_with

ใช้ตรวจว่าในประโยคจบด้วยคำที่กำหนดไหม

	$value = ends_with('This is my name', 'name');

### snake_case

แปลงคำให้ไปอยู่ในรูปแบบ `snake_case` คืออักษรขึ้นต้นคำเป็นตัวเล็กแล้วแบ่งคำด้วยเครื่องหมายอันเดอร์สกอร์

	$snake = snake_case('fooBar');

	// foo_bar

### starts_with

ใช้ตรวจว่าในประโยคขึ้นต้นด้วยคำที่กำหนดไหม

	$value = starts_with('This is my name', 'This');

### str_contains

ใช้ตรวจว่าในประโยคมีคำที่กำหนดไหม

	$value = str_contains('This is my name', 'my');

### str_finish

เพิมตัวอักษรที่กำหนดลงไปท้ายคำ

	$string = str_finish('this/string', '/');

	// this/string/

### str_is

ตรวจว่าค่าที่ป้อนเข้ามาตรงกับรูปแบบที่กำหนดไหม

	$value = str_is('foo*', 'foobar');

### str_plural

แปลงตัวอักษรจากเอกพจน์เป็นพหูพจ์ ปล.เติม s,es,ies 

	$plural = str_plural('car');

### str_random

สุ่มตัวอักษรขึ้นมาโดยกำหนดความยาวตามค่าที่ป้อนเข้ามา

	$string = str_random(40);

### str_singular

แปลงตัวอักษรจากเอกพจน์เป็นเอกพจน์

	$singular = str_singular('cars');

### studly_case

แปลงคำให้ไปอยู่ในรูปแบบ `StudlyCase` คืออักษรขึ้นต้นคำเป็นตัวใหญ่ถ้ามีเครื่องหมายอันเดอร์สกอร์ก็ลบออก

	$value = studly_case('foo_bar');

	// FooBar

### trans

ใช้แปลภาษาเหมือนกับใช้ `Lang::get`.

	$value = trans('validation.required'):

### trans_choice

แปลโดยเริ่มต้นจากคำที่กำหนดโดยนับต่อไปตามค่าจากตัวแปร $count เหมือนกับ `Lang::choice`.

	$value = trans_choice('foo.bar', $count);

<a name="urls"></a>
## URLs
เป็นฟังก์ชันที่ใช้จัดการ URL
### action

สร้างลิ้งจาก controller

	$url = action('HomeController@getIndex', $params);

### asset

สร้างลิ้งจากไฟล์ที่อยู่ในโฟลเดอร์ asset

	$url = asset('img/photo.jpg');

### link_to

สร้างลิ้งโดยกำหนดค่าต่างๆเอง

	echo link_to('foo/bar', $title, $attributes = array(), $secure = null);

### link_to_asset

สร้างลิ้งจากไฟล์ที่อยู่ในโฟลเดอร์ asset

	echo link_to_asset('foo/bar.zip', $title, $attributes = array(), $secure = null);

### link_to_route

สร้างลิ้งโดยอ้างอิงจากค่า route

	echo link_to_route('route.name', $title, $parameters = array(), $attributes = array());

### link_to_action

สร้างลิ้งเข้าไปหาฟังก์ชันใน controller

	echo link_to_action('HomeController@getIndex', $title, $parameters = array(), $attributes = array());

### secure_asset

สร้างลิ้งจากไฟล์ที่อยู่ในโฟลเดอร์ asset โดยใช้ https

	echo secure_asset('foo/bar.zip', $title, $attributes = array());

### secure_url

สร้างลิ้งที่เป็น https

	echo secure_url('foo/bar', $parameters = array());

### url

สร้างลิ้งจากการกำหนดเอง

	echo url('foo/bar', $parameters = array(), $secure = null);

<a name="miscellaneous"></a>
## Miscellaneous
ฟังก์ชันอเนกประสงค์
### csrf_token

สร้างค่า hash ที่ใช้ป้องกันการโจมตีแบบ csrf

	$token = csrf_token();

### dd

ใช้ในการดึงค่าทั้งหมดในตัวแปรออกมาแสดง

	dd($value);

### value
ดึงค่าจากฟังก์ชันที่ไม่มีชื่อ

	$value = value(function() { return 'bar'; });

### with

ใช้ดึงค่ากลับมาเป็นวัตถุ

	$value = with(new Foo)->doWork();
