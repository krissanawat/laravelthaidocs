# Queues

คลาสที่ช่วยเรียงลำดับการทำงานของฟังก์ชันต่างๆ

<a name="configuration"></a>
## การตั้งค่า

 Laravel Queue เตรียมฟังก์ชันที่ใช้ในการเข้าถึง api ของเว็บที่ให้บริการคิวไว้
การคิวคือการเรียงลำดับงานของเว็บไซต์ เช่น เรามีเมล์ที่ต้องส่งถึง 1000 ฉบับถ้าส่งแบบเดิม server อาจจะรับไม่ไหว เราจึงมีคลาส queue มาเพื่อการนี้ครับ

ไฟล์ที่ใช้ตั้งค่าเก็บไว้ที่ `app/config/queue.php`. ในไฟล์จะมีข้อมูลที่เราต้องใช้ในการเชื่อมต่อผู้ให้บริการคิว เช่น [Beanstalkd](http://kr.github.com/beanstalkd), [IronMQ](http://iron.io), [Amazon SQS](http://aws.amazon.com/sqs), and synchronous (สำหรับการทดสอบในเครื่อง) 

ชื่อคลาสของผู้ให้บริการคิวที่เราจะป้อนเข้าไป:

- Beanstalkd: `pda/pheanstalk`
- Amazon SQS: `aws/aws-sdk-php`
- IronMQ: `iron-io/iron_mq`

<a name="basic-usage"></a>
## การใช้งานเบื้องต้น

ในการส่งงานใหม่เข้าไปในคิวเราใช้ฟังก์ชัน `Queue::push`

**Pushing A Job Onto The Queue**

	Queue::push('SendEmail', array('message' => $message));

พารามิเตอร์ตัวแรกเป็น ฟัง์ชันที่เราใช้ควบคุมคิวนี้. ตัวที่สองเป็นข้อมูลที่เราจะทำการคิว 

**ตัวอย่างฟังก์ชันที่ใช้ควบคุมการคิว**

	class SendEmail {

		public function fire($job, $data)
		{
			//
		}

	}

ฟังก์ชัน`fire`รับพารามิเตอร์ `Job` ตัวที่สองคือข้อมูล `data` ที่จะส่งลงคิว

ถ้าเราไม่ใช่เมทอด `fire`เราสามารถกำหนดได้ตามตัวอย่างครับ

**เปลี่ยนจาก fire เป็น Push**

	Queue::push('SendEmail@send', array('message' => $message));

เมื่อมีการทำงานไปเเล้วเราก็ต้องลบข้อมูลออกไปโดยใช้เมทอด `delete` เพื่อลบ `Job` instance:

**ตัวอย่างการลบ**

	public function fire($job, $data)
	{
		// Process the job...

		$job->delete();
	}

ถ้าเราต้องการเอางานที่ทำไปกลับมาเข้าคิวอีกครั้งก็ใช้เมทอด `release` ครับ

**ตัวอย่าง**

	public function fire($job, $data)
	{
		// Process the job...

		$job->release();
	}

เราสามารถกำหนดเวลาที่จะหน่วงไว้ก่อนที่จะสั่งให้งานต่อไปทำงานได้แบบนี้ครับ

	$job->release(5);

เมื่องานที่เข้าคิวเกิดข้อผิดพลาดขึ้น จะถูกนำกลับไปต่อคิวใหม่ เราสามารถตรวจสอบการทำงานใหม่ได้โดยฟังก์ชัน `attempts`

**ตรวจหางานที่มีการพยายามทำมากกว่า 3 ครั้ง**

	if ($job->attempts() > 3)
	{
		//
	}



**การเรียกข้อมูลของาน**

	$job->getJobId();

<a name="queueing-closures"></a>
## Queueing Closures

เราสามารถใช้งานฟังก์ชันที่ไม่มีชื่อในการสร้างงานได้โดยตัวอย่างเลยครับ


	Queue::push(function($job) use ($id)
	{
		Account::delete($id);

		$job->delete();
	});

> **หมายเหตุ:** เมื่อใช้ฟังก์ชันที่ไม่มีชื่อกับ queue ตัวแปร `__DIR__` และ `__FILE__` จะไม่สามารถใช้งานได้

ถ้าใช้บริการของ Iron.io [push queues](#push-queues), ควรจะไม่ใช้งานฟังก์ชันที่ไม่มีชื่อในเมล์. จะมีการตรวจสอบคำร้องว่าส่งมาจาก Iron.io.จริงหรือไม่ ตัวอย่าง `https://yourapp.com/queue/receive?token=SecretToken`.ควรทำการตรวจสอบค่า secrettoken ก่อนทำการคิว.

<a name="running-the-queue-listener"></a>
## Running The Queue Listener

Laravel เตรียมคำสั่ง`queue:listen` เพื่อรอรับคำขอที่มากจากผู้ให้บริการ

**ตัวอย่างการใช้งาน**

	php artisan queue:listen

เราสามารถกำหนดค่าการเชื่อมต่อเสริมลงไปได้:

	php artisan queue:listen connection

เราสามารถใช้โปรแกรม [Supervisor](http://supervisord.org/) เพื่อตรวจสอบว่าคำสั่ง queue listener ยังทำงานอยู่ไหม

สามารถตั้งค่าเวลาที่จะอนุญาตุให้เกิดทำงานขึ้นได้

**ตัวอย่างการหน่วงเวลา**

	php artisan queue:listen --timeout=60


**สั่งให้งานชิ้นแรกที่อยู่บนคิวทำงาน**

	php artisan queue:work

<a name="push-queues"></a>
## Push Queues

Push queues คือการโยนภาระการรอรับคิวจากที่ทำบนเครื่องขิงเรา เปลี่ยนไปให้ผู้บริการทำแทน ซึ่งมี [Iron.io](http://iron.io) เจ้าเดียวที่สนับสนุน. ก่อนอื่นเราต้องไปสมัครบริการของ Ironio ก่อน, แล้วนำข้อมูลการเชื่อมต่อมาใส่ไว้ที่ `app/config/queue.php`

ต่อมาก็ใช้คำสั่ง Artisan `queue:subscribe`  เพื่อลงทะเบียนคิวไว้กับ Iron.io:

**ตัวอย่าง**

	php artisan queue:subscribe queue_name http://foo.com/queue/receive

ตอนนี้ ถ้าเราไปดูที่หน้าแสดงผลคิวที่ Iron.io , ก็จะเห็นรายการคิว และรายชื่อลิ้งที่เราจะสั่งให้ทำงาน. เราสามารถลงทะเบียนไว้ทีละหลายๆลิ้งก็ได้.ต่อมาเราต้องมาสร้าง route เพื่อรับคำร้องขอของ Iron.io โดยชื่อ route จะเป็น `queue/receive` และส่งค่ากลับไปด้วยฟังก์ชัน `Queue::marshal`

	Route::post('queue/receive', function()
	{
		return Queue::marshal();
	});

เมทอด `marshal` จะดูแลการทำงานของคิวให้