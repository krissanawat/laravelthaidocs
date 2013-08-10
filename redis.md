# Redis

[Redis](http://redis.io) เป็นโอเพ่นซอร์สฐานข้อมูลขนาดเล็ก ที่เก็บข้อมูลในรูปแบบ คีย์กับค่า หนึ่งค่าต้องมีคีย์กำกับ ประมาณนี้ครับ อยากรู้จักมากกว่านี้ต้องลองไปเล่นที่ [try redis](http://try.redis.io/) โดยข้อมูลที่อยู่ในนี้จะมี [strings](http://redis.io/topics/data-types#strings), [hashes](http://redis.io/topics/data-types#hashes), [lists](http://redis.io/topics/data-types#lists), [sets](http://redis.io/topics/data-types#sets), และ [sorted sets](http://redis.io/topics/data-types#sorted-sets).

<a name="configuration"></a>
## การปรับแต่ง

การปรับแต่ง redis เราทำใน **app/config/database.php** ในนี้เราจะเห็นอาเรย์ชื่อ  **redis** แบบนี้ครับ

	'redis' => array(

		'cluster' => true,

		'default' => array('host' => '127.0.0.1', 'port' => 6379),

	),

โดยค่าเริ่มต้นเเล้ว ถูกตั้งให้สนับสนุนการพ้ฒนาบนเครื่อง เราสามารถปรับแต่งได้ตามใจครับ
อาเรย์ชื่อ `cluster` ใช้เพื่อบอกให้ redis ที่อยู่ในเครื่องทำการ client-side sharding ข้าม Redis nodes ของคุณ, ทำให้เราสามารถสร้าง ram จำนวนมากได้. แต่การทำ client-side sharding แต่เราไม่สามารถจับการ failover ได้



<a name="usage"></a>
## การใช้งาน

เราสามารถสร้างตัวแปรที่เป็นตัวแทนของเมทอด `Redis::connection` โดยการ

	$redis = Redis::connection();

เรายังไม่ได้กำหนดชื่อ server redis ของเราต้องกำหนดก่อนนะครับ

	$redis = Redis::connection('other');

ถ้าอยากศึกษาต่อไปที่นี้ครับ [Redis commands](http://redis.io/commands) ในการจัดการต่างๆ laravel มีฟังก์ชันต่างๆ มาให้เเล้วครับ:

	$redis->set('name', 'Taylor');

	$name = $redis->get('name');

	$values = $redis->lrange('names', 5, 10);

ถ้าไม่ต้องการใช้คำสั่ง laravel ก็สามารถใช้เมทอด `command` ในการใช้คำสั่งของ redis ตรงๆได้

	$values = $redis->command('lrange', array(5, 10));

คลาส `Redis` ที่เป็นตัว static คลาสครับ:

	Redis::set('name', 'Taylor');

	$name = Redis::get('name');

	$values = Redis::lrange('names', 5, 10);


<a name="pipelining"></a>
## Pipelining

Pipelining คือการส่งคำสั่งหลายๆ ตัวไปยัง redis server laravel มีเมทอด `pipeline` มาให้ใช้

**ตัวอย่าง**

	Redis::pipeline(function($pipe)
	{
		for ($i = 0; $i < 1000; $i++)
		{
			$pipe->set("key:$i", $i);
		}
	});