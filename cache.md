# Cache


<a name="configuration"></a>
### การตั้งค่า

Laravel เตรียมรูปแบบของการ ` cache` ไว้ให้เราใช้เเล้วหลายตัวเลยนะครับ . ซึ่งรายชื่อและการตั้งค่าอยู่ที่ `app/config/cache.php`. ในไฟล์เราสามารถตั้งค่าให้กับ cache ที่เราชื่นชอบได้เลย Laravel สนับสนุนทั้ง [Memcached](http://memcached.org) กับ [Redis](http://redis.io) อยู่แล้วครับ.

ก่อนจะทำการตั้งค่าใดๆ ลองไปศึกษาก่อนนะครับ ตัว laravel ใช้ `file` cache เป็นตัวเริ่มต้นนะครับ  ถ้าเว็บไซต์มีขนาดใหญ่ควรเปลี่ยนไปใช้ `Memcached` หรือ `APC` จะดีกว่า


<a name="cache-usage"></a>
### การใช้งาน Cache 

----------

**การเก็บค่าลง cache**

	Cache::put('key', 'value', $minutes);

**เก็บลงในกรณีที่ไม่มีค่านั้นอยู่**

	Cache::add('key', 'value', $minutes);

**ตรวจว่ามีค่าไหม**

	if (Cache::has('key'))
	{
		//
	}

**ดึงค่าจากแคช**

	$value = Cache::get('key');

**ดึงค่าโดยเรียกค่าเริ่มต้น**

	$value = Cache::get('key', 'default');

	$value = Cache::get('key', function() { return 'default'; });

**กำหนดให้ค่าชนิดนี้ไม่มีวันหมดอายุ**

	Cache::forever('key', 'value');

บางเวลาเราต้องการเรียกใช้ค่าจากแคช แต่ไม่มีค่าแล้ว เราสามารถเรียกใช้ `Cache::remember` โดยการดึงค่าจากฐานข้อมูลขึ้นมาเก็บไว้ในแคช

	$value = Cache::remember('users', $minutes, function()
	{
		return DB::table('users')->get();
	});

และยังสามารถผสมคำสั่ง `remember` กับ `forever` 

	$value = Cache::rememberForever('users', function()
	{
		return DB::table('users')->get();
	});

เราสามารถเก็บค่าชนิดใดก็ได้เพราะรูปแบบการเก็บค่าใช้แบบ `serialize`

**การลบค่าออกจากคีย์**

	Cache::forget('key');

<a name="increments-and-decrements"></a>
## การเพิ่มและการลดค่า

แคชทุกชนิดยกเว้น `file` กับ `database` สนับสนุนการทำเพิ่มและลดค่าของแคช

**การเพิ่มค่า**

	Cache::increment('key');

	Cache::increment('key', $amount);

**การลดค่า**

	Cache::decrement('key');

	Cache::decrement('key', $amount);

<a name="cache-sections"></a>
## Cache Sections

> **หมายเหตุ:** Cache sections ไม่สนับสนุนแคชแบบ `file` หรือ `database` 

Cache sections คือการให้เราสามารถจับกลุ่มให้กับแคชที่มีรูปแบบการเก็บค่าที่คล้ายๆ กันโดยใช้คำสั่ง`section` 

**ตัวอย่างการใช้งาน Cache section**

	Cache::section('people')->put('John', $john);

	Cache::section('people')->put('Anne', $anne);

**การเข้าถึงค่าของแคช**

	$anne = Cache::section('people')->get('Anne');

flush คือการลบค่าออก:

	Cache::section('people')->flush();

<a name="database-cache"></a>
## การเก็บแคชไว้ในฐานข้อมูล

เมื่อเราจะใช้ฐานข้อมูลเก็บค่าแคช เราต้องเพิ่มตารางที่จะใช้เก็บก่อน นี่คือตัวอย่างการสร้างตารางที่ใช้เก็บแคชในรูปแบบ ของ laravel 

	Schema::create('cache', function($table)
	{
		$table->string('key')->unique();
		$table->text('value');
		$table->integer('expiration');
	});
