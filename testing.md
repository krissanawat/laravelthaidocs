# Unit Testing

Laravel สร้างขึ้นมาด้วยแนวคิดของการทดสอบเป็นเบื้องต้นอยู่เเล้วครับ โดยหลักแล้วจะสนับสนุนไลบราลี่ PHPUnit เป็นพื้นฐาน และ `phpunit.xml`ไฟล์ได้ถูกเตรียมการเอาไว้ให้แล้ว. Laravel เตรียมคลาส Symfony HttpKernel, DomCrawler, และ BrowserKit components ที่อนุญาตให้เราจำลองบราวเซอร์ขึ้นมาเเละเข้าไปแก้ไขไฟล์ html ได้
ตัวอย่างไฟล์อยู่ที่โฟลเดอร์ `app/tests` 
<a name="defining-and-running-tests"></a>
## Defining & Running Tests

การสร้างไฟล์สำหรับทดสอบนั้นเราจะไปสร้างที่โฟลเดอร์ `app/tests` สร้างคลาสที่สืบทอดคลาส `TestCase`.

**ตัวอย่างคลาสสำหรับใช้ทดสอบ**

	class FooTest extends TestCase {

		public function testSomethingIsTrue()
		{
			$this->assertTrue(true);
		}

	}
เราจะทำการทดสอบโดยรัน `phpunit` บน commandline

> **หมายเหตุ:** ถ้าคุณประกาศเมทอด `setUp` มั่นใจว่าได้เรียก `parent::setUp`แล้ว

<a name="test-environment"></a>
## สภาวะการตั้งค่าสำหรับการทดสอบ

เมื่อใช้งาน unit tests, Laravel จะทำการเปลี่ยนการสภาวะการตั้งค่าให้ไปเป็น `testing`. และจะตัดการทำงานของ `session` และ `cache` หมายความว่าจะไม่มีแคชและ session เกิดขึ้นระหว่างการทดสอบ

<a name="calling-routes-from-tests"></a>
## การเรียก Routes ในขณะทดสอบ


**ตัวอย่างการเรียก Route ในขณะทำกาารทดสอบ**

	$response = $this->call('GET', 'user/profile');

	$response = $this->call($method, $uri, $parameters, $files, $server, $content);

เราสามารถตรวจสอบออปเจค `Illuminate\Http\Response`

	$this->assertEquals('Hello World', $response->getContent());


**ตัวอย่างการเรียก Controller ในขณะทดสอบ**

	$response = $this->action('GET', 'HomeController@index');

	$response = $this->action('GET', 'UserController@profile', array('user' => 1));

เมทอด `getContent` จะส่งค่าเป็นตัวอักษรกลับคืนมา `View` เราสามารถเข้าถึงได้ด้วยตัวแปร `original` 

	$view = $response->original;

	$this->assertEquals('John', $view['name']);

ถ้าจะเรียก HTTPS route,เราต้องใช้เมทอด`callSecure`

	$response = $this->callSecure('GET', 'foo/bar');

### DOM Crawler

คลาส DOM Crawler ทำให้เราสามารถตรวจสอบ html ที่ถูกสร้างขึ้นมาระหว่างการทดสอบได้ ตัวอย่างการใช้

	$crawler = $this->client->request('GET', '/');

	$this->assertTrue($this->client->getResponse()->isOk());

	$this->assertCount(1, $crawler->filter('h1:contains("Hello World!")'));

<a name="mocking-facades"></a>
## Mocking Facades

เมื่อเราทำการทดสอบ,เราจะทำการจำลองในการเรีกคลาส Facade ตัวอย่างเราจะทำการเรียก controller

	public function getIndex()
	{
		Event::fire('foo', array('name' => 'Dayle'));

		return 'All done!';
	}

เราสามารถจำลองคลาส`Event`โดยใช้เมทอด `shouldReceive` 

**การจำลองคลาส Facade**

	public function testGetIndex()
	{
		Event::shouldReceive('fire')->once()->with(array('name' => 'Dayle'));

		$this->call('GET', '/');
	}

> **หมายเหตุ:** คุณไม่ควรจำลองคลาส `Facade Request` ใช้เมทอด `call` ดีกว่าครับ

<a name="framework-assertions"></a>
## Framework Assertions

เมทอด `assert` ใช้ในการตรวจสอบว่าค่าที่ออกมาตรงกับที่เราคาดหวังไว้ไหม

**คาดหวังว่าค่าที่ส่งมาจะไม่ผิดพลาด**

	public function testMethod()
	{
		$this->call('GET', '/');

		$this->assertResponseOk();
	}

**คาดหวังว่าจะเป็น 403**

	$this->assertResponseStatus(403);

**คาดหวังว่าฟังก์ชันจะส่งกลับไปที่ route**

	$this->assertRedirectedTo('foo');

	$this->assertRedirectedToRoute('route.name');

	$this->assertRedirectedToAction('Controller@method');

**คาดหวังว่าในหน้า view จะมีค่า**

	public function testMethod()
	{
		$this->call('GET', '/');

		$this->assertViewHas('name');
		$this->assertViewHas('age', $value);
	}

**คาดหวังว่าใน session จะมีค่า**

	public function testMethod()
	{
		$this->call('GET', '/');

		$this->assertSessionHas('name');
		$this->assertSessionHas('age', $value);
	}

<a name="helper-methods"></a>
## Helper Methods

คลาส `TestCase` มีเมทอดช่วยให้เราทำการทดสอบได้ง่ายๆ เยอะเลยครับ.

เมทอด `be`ใช้ในการจำลองการล็อกอิน

**ตัวอย่าง**

	$user = new User(array('name' => 'John'));

	$this->be($user);


**ทำการเพิ่มข้อมูลลงฐานข้อมูลในขณะทดสอบ**

	$this->seed();

	$this->seed($connection);

