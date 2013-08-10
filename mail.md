# Mail

<a name="configuration"></a>
## การตั้งค่า

Laravel นำไลบราลี่ [SwiftMailer](http://swiftmailer.org) มาใช้งาน การตั้งค่าอยู่ที่ `app/config/mail.php`,โดยจะให้เราเปลี่ยน SMTP host, port, และ username กับ password, แล้วก็ค่า `from` คือค่าเริ่มต้นของชื่อผู้รับ. ถ้าเราต้องการใช้ไลบรารี  `php mail` ในการส่งก็เพียงเปลี่ยน `driver` เป็น `mail` 

<a name="basic-usage"></a>
## การใช้งานเบื้องต้น

ฟังก์ชัน `Mail::send` ใช้ในการส่งอีเมล์

	Mail::send('emails.welcome', $data, function($message)
	{
		$message->to('foo@example.com', 'John Smith')->subject('Welcome!');
	});

เมทอด `send` ตัวแปรแรกคือไฟล์ html ที่เป็นรูปแบบข้อมความในเมล์. ตัวที่สองคือข้อมูลที่จะเขียนลงเมล์ `$data` ซึ่งจะถูกส่งไปยัง view ตัวที่สามเป็นฟังก์ชันที่ใช้กำหนดค่าต่างๆของอีเมล์

> **Note:** ตัวแปร `$message` คือออปเจ็คของตัว Swiftmailer class ซึ่งเราจะใช้กำหนดค่าต่างๆของเมล์


	Mail::send(array('html.view', 'text.view'), $data, $callback);

ตัวอย่างคือเราเลือกที่จะส่งไปในรูปแบบใด `html` หรือ `text`

	Mail::send(array('text' => 'view'), $data, $callback);

ตัวอย่างการปรับแต่งเนื้อหาภายในเมล์:

	Mail::send('emails.welcome', $data, function($message)
	{
		$message->from('us@example.com', 'Laravel');

		$message->to('foo@example.com')->cc('bar@example.com');

		$message->attach($pathToFile);
	});

เมื่อจะทำการแนบไฟล์เราต้องใส่นามสกุลกับชื่อให้มันด้วย:

	$message->attach($pathToFile, array('as' => $display, 'mime' => $mime));


<a name="embedding-inline-attachments"></a>
## การแทรกไฟล์ไว้ระหว่างบรรทัด

เราสามารถแนบรูปไปโดยไม่ให้เเสดงได้โดยใช้ฟังก์ชัน `embed`

**ตัวอย่างการใช้งาน**

	<body>
		Here is an image:

		<img src="<?php echo $message->embed($pathToFile); ?>">
	</body>




<a name="queueing-mail"></a>
## เรียงลำดับการส่งอีเมล์

Laravel เตรียมคลาส [Queue](/docs/queues) มาให้เราใช้ในการเรียงลำดับการส่งอีเมล์

**ตัวอย่าง**

	Mail::queue('emails.welcome', $data, function($message)
	{
		$message->to('foo@example.com', 'John Smith')->subject('Welcome!');
	});

เราสามารถหน่วงเวลาการส่งโดยใช้ฟังก์ชัน `later` ตามตัวอย่างครับ

	Mail::later(5, 'emails.welcome', $data, function($message)
	{
		$message->to('foo@example.com', 'John Smith')->subject('Welcome!');
	});

ถ้าเรามีหลายคิว มีฟังก์ชันให้เราเรียงคิวอีก คือ `queueOn` และ `laterOn` 

	Mail::queueOn('queue-name', 'emails.welcome', $data, function($message)
	{
		$message->to('foo@example.com', 'John Smith')->subject('Welcome!');
	});

<a name="mail-and-local-development"></a>
## Mail & Local Development

ในการพัฒนานั้น เรายังไม่ต้องใช้งานเมล์จริงๆในการส่งก็ได้ laravel เตรียมฟังก์ชัน `Mail::pretend` หรือตั้งค่า `pretend` ใน `app/config/mail.php` เป็น `true`. เพื่อเข้าสู่ `pretend` mode ข้อความบนเมล์ที่ถูกส่งจะถูกเขียนบนล็อกแทย
