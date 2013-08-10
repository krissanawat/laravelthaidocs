# Schema Builder

คลาส `Schema`ใช้ในการจัดการตาราง มักใช้ร่วมกับคำสั่ง migration

<a name="creating-and-dropping-tables"></a>
## สร้างและลบตาราง

สร้างตารางใหม่ด้วยเมทอด `Schema::create` ตามตัวอย่างเลยครับ

	Schema::create('users', function($table)
	{
		$table->increments('id');
	});

เมทอดตัวแรกคือชื่อตาราง พารามิเตอร์ที่สองคือ `Closure` ที่จะรับออปเจคของคลาส `Blueprint` ในการสร้างตาราง

จะเปลี่ยนชื่อตารางใช้เมทอด`rename`

	Schema::rename($from, $to);

ในการเลือกการเชื่อมต่อกรณีเรามีหลายฐานข้อมูลใช้เมทอด `Schema::connection` 

	Schema::connection('foo')->create('users', function($table)
	{
		$table->increments('id'):
	});

จะลบตารางใช้เมทอด `Schema::drop` 

	Schema::drop('users');

	Schema::dropIfExists('users');

<a name="adding-columns"></a>
## เพิ่มคอลัมน์

ในการแก้ไขตารางเราใช้คำสั่ง `Schema::table` 

	Schema::table('users', function($table)
	{
		$table->string('email');
	});

ตารางคำสั่งต่างของคลาส `Blueprint` ครับ

Command  | Description
------------- | -------------
`$table->increments('id');`  |  ใช้ทำ auto_increment ให้  (primary key).
`$table->string('email');`  |  ค่าที่ออกมาจะเป็น VARCHAR ความยาว 255
`$table->string('name', 100);`  |  ค่าที่ออกมาจะเป็น VARCHAR ความยาว 100
`$table->integer('votes');`  |  ค่าที่ออกมาจะเป็น interger
`$table->bigInteger('votes');`  |  ค่าที่ออกมาจะเป็น bigint
`$table->smallInteger('votes');`  |  ค่าที่ออกมาจะเป็นSMALLINT
`$table->float('amount');`  |  ค่าที่ออกมาจะเป็น FLOAT
`$table->decimal('amount', 5, 2);`  |   ค่าที่ออกมาจะเป็น decimal ที่มีค่าระหว่าง 5,2
`$table->boolean('confirmed');`  |   ค่าที่ออกมาจะเป็น  BOOLEAN
`$table->date('created_at');`  |   ค่าที่ออกมาจะเป็น DATE 
`$table->dateTime('created_at');`  |   ค่าที่ออกมาจะเป็นDATETIME
`$table->time('sunrise');`  |    ค่าที่ออกมาจะเป็น TIME
`$table->timestamp('added_on');`  |   ค่าที่ออกมาจะเป็น TIMESTAMP 
`$table->timestamps();`  | เพิ่มคอลัมน์ **created\_at** และ **updated\_at** columns
`$table->softDeletes();`  |  เพิ่มคอลัมน์ **deleted\_at** 
`$table->text('description');`  |  ค่าที่ออกมาจะเป็น TEXT 
`$table->binary('data');`  |  ค่าที่ออกมาจะเป็น BLOB 
`$table->enum('choices', array('foo', 'bar'));` |   ค่าที่ออกมาจะเป็น ENUM
`->nullable()`  |  อนุญาตให้เป็นค่าว่างได้
`->default($value)`  |  สร้างค่าเริ่มต้น
`->unsigned()`  |  เลี่ยน INTEGER เป็น UNSIGNED

ถ้าเราใช้ MySQL dสามารถใช้เมทอด `after` ทำการเรียงลำดับคอลัมน์

**ตัวอย่าง**

	$table->string('name')->after('email');

<a name="renaming-columns"></a>
## เปลี่ยนชื่อ Columns

ใช้เมทอด `renameColumn` ครับ

**ตัวอย่าง**

	Schema::table('users', function($table)
	{
		$table->renameColumn('from', 'to');
	});

> **หมายเหตุ:** คอลัมน์ชนิด `enum` ไม่สนับสนุน

<a name="dropping-columns"></a>
## ลบ Columns

**ตัวอย่าง**

	Schema::table('users', function($table)
	{
		$table->dropColumn('votes');
	});

**ลบหลายๆ คอลัมน์**

	Schema::table('users', function($table)
	{
		$table->dropColumn('votes', 'avatar', 'location');
	});

<a name="checking-existence"></a>
## ตรวจว่าตารางมีอยู่ไหม

**ตรวจว่าตารางมีอยู่ไหม**

	if (Schema::hasTable('users'))
	{
		//
	}

**ตรวจว่าคอลัมน์มีอยู่ไหม**

	if (Schema::hasColumn('users', 'email'))
	{
		//
	}

<a name="adding-indexes"></a>
## เพิ่ม Indexes

**สร้างทั้ง Column และ Index**

	$table->string('email')->unique();


Command  | Description
------------- | -------------
`$table->primary('id');`  |  เพิ่ม primary key
`$table->primary(array('first', 'last'));`  |  เพิ่ม composite keys
`$table->unique('email');`  | เพิ่ม unique index
`$table->index('state');`  |  เพิ่ม basic index

<a name="foreign-keys"></a>
## Foreign Keys คีย์เชื่อม


**ตัวอย่างการเพิ่มคีย์เชื่อม**

	$table->foreign('user_id')->references('id')->on('users');

ในตัวอย่างเราทำการให้คอลัมน์ `user_id`อ้างอิงกับคอลัมน์ `id` บนตาราง`users` 

เราสามารถเสริมคำสั่ง "on delete" และ "on update" เข้าไปเหมือนตัวอย่าง

	$table->foreign('user_id')
          ->references('id')->on('users')
          ->onDelete('cascade');

ในการลบใช้เมทอด `dropForeign`  ตัวอย่าง

	$table->dropForeign('posts_user_id_foreign');

> **หมายเหตุ:** ให้ชนิดของคอลัมน์ที่เป็นคีย์เชื่อมให้เป็น `unsigned` ทุกครั้งกรณีที่เชื่อมไปยัง คอลัมน์ที่เป็น interger และเป็น auto_increment ครับ

<a name="dropping-indexes"></a>
## ลบ Indexes

ในการลบคีย์เชื่อม larave ตั้งค่าคีย์เป็นค่าเริ่มต้นให้เเล้วนะครับ โดยอ้างอิงจากชนิดกับชื่อของตารง

Command  | Description
------------- | -------------
`$table->dropPrimary('users_id_primary');`  |  ลบคีย์หลักจากตาราง users
`$table->dropUnique('users_email_unique');`  |  ลบคีย์เดี่ยวจากตาราง users
`$table->dropIndex('geo_state_index');`  |  ลบคีย์ทั่วไปจาก ตาราง geo
<a name="storage-engines"></a>
##ชนิดของตาราง

การเซตชนิดของตารางเราใช้เมทอด `engine` ตามตัวอย่างครับ

    Schema::create('users', function($table)
    {
        $table->engine = 'InnoDB';

        $table->string('email');
    });
