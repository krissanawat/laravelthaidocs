# Query Builder
คือการจัดการๆคิวรี่ของ laravel ช่วยอำนวยความสะดวกให้เรา ไม่ต้องเขียนคิวรี่ยาวๆ ด้วยตัวเองครับ

<a name="selects"></a>
## การเลือกข้อมูล

**ดึงค่าทั้งหมดจากตาราง users**

	$users = DB::table('users')->get();

	foreach ($users as $user)
	{
		var_dump($user->name);
	}

**ดึงค่าแถวแรกจากตาราง users โดย name เท่ากับ john**

	$user = DB::table('users')->where('name', 'John')->first();

	var_dump($user->name);

**ดึงค่าจากตาราง users โดย name เท่ากับ john แล้วก็เอาแค่คอลัมน์ที่ชื่อว่า name**

	$name = DB::table('users')->where('name', 'John')->pluck('name');

**ดึงค่าทั้งหมดจากตาราง roles โดยเอาแค่คอลัมน์ title**

	$roles = DB::table('roles')->lists('title');

ค่าที่คืนมาจะเป็นอาเรย์นะครับ ถ้าเราอยากใส่คีย์ให้แต่ละแถวเราใส่พารามิเตอร์ตัวที่สองเข้าไป name จะไปเป็นคีย์ให้กับ title

	$roles = DB::table('roles')->lists('title', 'name');

**เมทอด select ใช้กำหนดคำสั่งในการเลือกเอง**

	$users = DB::table('users')->select('name', 'email')->get();

	$users = DB::table('users')->distinct()->get();

	$users = DB::table('users')->select('name as user_name')->get();

**เลือกข้อมูลจากผลการคิวรี่อีกที**

	$query = DB::table('users')->select('name');

	$users = $query->addSelect('age')->get();

**การใช้ where**

	$users = DB::table('users')->where('votes', '>', 100)->get();

**การใช้หลายๆเงื่อนไขโดยวิธีเชนเมทอด**

	$users = DB::table('users')
	                    ->where('votes', '>', 100)
	                    ->orWhere('name', 'John')
	                    ->get();

**ใช้ between**

	$users = DB::table('users')
	                    ->whereBetween('votes', array(1, 100))->get();

**ตัวอย่างการใช้ where กับ In ร่วมกัน**

	$users = DB::table('users')
	                    ->whereIn('id', array(1, 2, 3))->get();

	$users = DB::table('users')
	                    ->whereNotIn('id', array(1, 2, 3))->get();

**ใช้หาแถวที่เป็นค่าวางตามคอลัมน์**

	$users = DB::table('users')
	                    ->whereNull('updated_at')->get();

**ตัวอย่างการเรียงลำดับข้อมูล**

	$users = DB::table('users')
	                    ->orderBy('name', 'desc')
	                    ->groupBy('count')
	                    ->having('count', '>', 100)
	                    ->get();

**การจำกัดข้อมูล**

	$users = DB::table('users')->skip(10)->take(5)->get();

<a name="joins"></a>
## Joins

**ตัวอย่างการ join ครับ**

	DB::table('users')
	            ->join('contacts', 'users.id', '=', 'contacts.user_id')
	            ->join('orders', 'users.id', '=', 'orders.user_id')
	            ->select('users.id', 'contacts.phone', 'orders.price');

การจอยแบบเติมคิวรี่ลงไปช่วยประหยัดเวลา เวลาคิดไม่ออกว่าจะใช้ฟังก์ไหนดี แทรกคิวรี่ลงไปตรงๆ เลย:

	DB::table('users')
	        ->join('contacts', function($join)
	        {
	        	$join->on('users.id', '=', 'contacts.user_id')->orOn(...);
	        })
	        ->get();

<a name="advanced-wheres"></a>
## Advanced Wheres

**การ where แบบ หลายเงื่อนไข**

	DB::table('users')
	            ->where('name', '=', 'John')
	            ->orWhere(function($query)
	            {
	            	$query->where('votes', '>', 100)
	            	      ->where('title', '<>', 'Admin');
	            })
	            ->get();

หน้าตาของคิวรี่จะออกมาเป็นแบบนี้

	select * from users where name = 'John' or (votes > 100 and title <> 'Admin')

**หาว่ามีค่านี้อยู่ไหม**

	DB::table('users')
	            ->whereExists(function($query)
	            {
	            	$query->select(DB::raw(1))
	            	      ->from('orders')
	            	      ->whereRaw('orders.user_id = users.id');
	            })
	            ->get();

หน้าตาของคิวรี่จะออกมาเป็นแบบนี้

	select * from users
	where exists (
		select 1 from orders where orders.user_id = users.id
	)

<a name="aggregates"></a>
## Aggregates การหาผลรวมชนิดต่างๆ


**ตัวอย่างการคำนวนค่า**

	$users = DB::table('users')->count();

	$price = DB::table('orders')->max('price');

	$price = DB::table('orders')->min('price');

	$price = DB::table('orders')->avg('price');

	$total = DB::table('users')->sum('votes');

<a name="raw-expressions"></a>
## Raw Expressions

Raw คือการใส่คิวรี่แบบสดๆ เข้าไปเลยไม่ต้องไปให้ฟังก์ชันสร้างให้ ประหยัดเวลามากขึ้นเมื่อเราต้องการค้นหาแบบซับซ้อน

**ตัวอย่างการใช้คำสั่งคิวรี่**

	$users = DB::table('users')
	                     ->select(DB::raw('count(*) as user_count, status'))
	                     ->where('status', '<>', 1)
	                     ->groupBy('status')
	                     ->get();

**การเพิ่มและลดค่าให้คอลัมน์**

	DB::table('users')->increment('votes');

	DB::table('users')->increment('votes', 5);

	DB::table('users')->decrement('votes');

	DB::table('users')->decrement('votes', 5);

การกำหนดค่าคอลัมน์ที่จะเพิ่มค่าให้

	DB::table('users')->increment('votes', 1, array('name' => 'John'));

<a name="inserts"></a>
## การเพิ่มข้อมูล 

**ตัวอย่าง**

	DB::table('users')->insert(
		array('email' => 'john@example.com', 'votes' => 0)
	);


**การเพิ่มค่าพร้อมกับเพิ่มค่า id ด้วย**

	$id = DB::table('users')->insertGetId(
		array('email' => 'john@example.com', 'votes' => 0)
	);

> **หมายเหตุ:** ถ้าใช้ PostgreSQL เมทอด insertGetId คาดหวังว่าจะใช้คอลัมน์ "id" เป็นตัวที่มันจะเพิ่มให้

**การเพิ่มหลายๆข้อมูล**

	DB::table('users')->insert(array(
		array('email' => 'taylor@example.com', 'votes' => 0),
		array('email' => 'dayle@example.com', 'votes' => 0),
	));

<a name="updates"></a>
## การแก้ไข

**ตัวอย่าง**

	DB::table('users')
	            ->where('id', 1)
	            ->update(array('votes' => 1));

<a name="deletes"></a>
## การลบ

**ตัวอย่าง**

	DB::table('users')->where('votes', '<', 100)->delete();

**การลบข้อมูลทั้งหมดเป็นการลบแบบแถวต่อแถว**

	DB::table('users')->delete();

**การลบแบบลบทั้งตารางด้วยแล้วสร้างขึ้นใหม่**

	DB::table('users')->truncate();

<a name="unions"></a>
## การทำ Unions

union คือการจับผลลัพท์ของการ selecet 2 ครั้ง มารวมกันเป็นหนึ่งผลลัพท์
**Performing A Query Union**

	$first = DB::table('users')->whereNull('first_name');

	$users = DB::table('users')->whereNull('last_name')->union($first)->get();

เมทอด `unionAll` ใช้งานเหมือนกับ `union` เลยครับ

<a name="caching-queries"></a>
## Caching Queries

เราสามารถทำการแคชหรือบันทึกผลการคิวรี่ไว้บน session ก่อนด้วยเมทอด `remember` 

**ตัวอย่าง**

	$users = DB::table('users')->remember(10)->get();

ในตัวอย่างเราจะแคชผลการค้นหานี้เป็นเวลา 10 นาที ระหว่างนี้การคิวรี่จากตัวอย่างจะไม่ไปดึงข้อมูลจากฐานข้อมูล แต่จะดึงจากแคชจนกว่าจะหมดเวลาครับ