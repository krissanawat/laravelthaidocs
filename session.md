# Session


<a name="configuration"></a>
## การตั้งค่าเบื้องต้น

ไฟล์ที่ใช้ตั้งค่าจะอยู่ที่ `app/config/session.php`.โดยชนิดของ session จะมีหลายชนิดนะครับแต่โดยเริ่มต้นแล้วจะเป็น `native` ส่วนการตั้งค่าอื่นๆ ก็จะเป็นเวลาที่จะให้ seesion มีชีวิตอยู่ ที่อยู่ของ seesion ชื่อของ cookie และอื่นๆ ครับ

<a name="session-usage"></a>
##การใช้งาน

**การสร้างค่าแล้วเก็บใน session**

	Session::put('key', 'value');

**ดึงค่าจาก Session**

	$value = Session::get('key');

**ดึงค่าเริ่มต้นของ session**

	$value = Session::get('key', 'default');

	$value = Session::get('key', function() { return 'default'; });

**ตรวจว่ามีค่านี้ใน Session หรือไม่**

	if (Session::has('users'))
	{
		//
	}

**ลบค่าออกจาก Session**

	Session::forget('key');

**ลบค่าทั้งหมด Session**

	Session::flush();

**สร้าง Session ID อีกครั้ง**

	Session::regenerate();

<a name="flash-data"></a>
## Flash Data

หลายๆครั้งเราต้องฝากค่าไว้ใน session เพื่อนำไปใช้ในการทำงานต่อไป สามารถใช้เมทอด `Session::flash` ตัวอย่าง

	Session::flash('key', 'value');

**ทำการเรียกใช้ falsh message อีกครั้ง**

	Session::reflash();

**ทำการเรียกใช้งานอีกครั้งเฉพาะค่า**

	Session::keep(array('username', 'email'));

<a name="database-sessions"></a>
## การเก็บ session ในฐานข้อมูล

เมื่อเราใช้ฐานข้อมูลเก็บ session เราต้องสร้างตารางขึ้นมาก่อน ด้วยคำสั่ง`Schema` ดังตัวอย่าง

	Schema::create('sessions', function($table)
	{
		$table->string('id')->unique();
		$table->text('payload');
		$table->integer('last_activity');
	});

ตอนนี้เราก็ใช้คำสั่ง `php artisab session:table` เป็นอันจบครับ