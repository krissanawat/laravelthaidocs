# การจัดการฐานข้อมูลเบื้องต้น


<a name="configuration"></a>
## การตั้งค่า

เราจะไปตั้งค่าที่ `app/config/database.php`. ซึ่งเราจะไปตั้งค่าการเชื่อมต่อข้างในนี้ครับ

laravel ในขณะนี้สนับสนุน: MySQL, Postgres, SQLite, and SQL Server.

<a name="running-queries"></a>
## การทำ Queries

เราใช้คลาส `DB` ในการเรียกใช้การคิวรี่นะครับ

**ตัวอย่างการคิวรี่**

	$results = DB::select('select * from users where id = ?', array(1));

ฟังก์ชัน`select`จะส่งค่าเป็น `array`กลับมาเสมอ

**ตัวอย่างการเพิ่มข้อมูล**

	DB::insert('insert into users (id, name) values (?, ?)', array(1, 'Dayle'));

**ตัวอย่างการแก้ไขข้อมูล**

	DB::update('update users set votes = 100 where name = ?', array('John'));

**ตัวอย่างการลบข้อมูล**

	DB::delete('delete from users');

> **หมายเหตุ:**  คำสั่ง `update` กับ `delete` จะคืนค่าเป็นจำนวนของแถวที่ได้ทำการจัดการไป.

**ตัวอย่างการใช้คำสั่งทั่วไป**

	DB::statement('drop table users');

เราสามารถกำหนดให้ฟังก์ชันนี้ทำงานเมื่อมีการคิวรี่เกิดขึ้นโดยใช้ฟังก์ชัน `DB::listen` 

**ตัวอย่างการใช้งาน**

	DB::listen(function($sql, $bindings, $time)
	{
		//
	});

<a name="database-transactions"></a>
## Database Transactions

เมื่อเราจะทำการคิวรี้หลายๆ คำสั่งเราจะใช้ฟังก์ชัน `transaction` ในการควบคุม เหมือนในตัวอย่างข้างล่าง

	DB::transaction(function()
	{
		DB::table('users')->update(array('votes' => 1));

		DB::table('posts')->delete();
	});

<a name="accessing-connections"></a>
## การเข้าถึงการเชื่อมต่อฐานข้อมูล

สมมุติเว็บของเราใช้ฐานข้อมูลหลายชนิด สามารถใช้ฟังก์ชัน `DB::connection` ในการเรียกฐานข้อมูล เฉพาะที่เราต้องการดังตัวอย่าง

	$users = DB::connection('foo')->select(...);

แล้วก็สามารถเชื่อมต่อใหม่ด้วยคำสั่ง reconnect 

	DB::reconnect('foo');

<a name="query-logging"></a>
## การเก็บประวัติการคิวรี่

โดยค่าเริ่มต้น laravel จะเก็บประวัติไว้ในหน่วยความจำอยู่เเล้ว ในกรณีที่เว็บของเรามีคำร้องขอจำนวนมาก การเก็บประวัติจะใช้หน่วยความจำมาก เราจะใช้ฟังก์ชัน `disableQueryLog` เพื่อทำการหยุดเก็บประวัติ ตัวอย่าง

	DB::connection()->disableQueryLog();