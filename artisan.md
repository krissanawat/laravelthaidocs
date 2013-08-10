# Artisan CLI


<a name="introduction"></a>
## Introduction

`Artisan` คือ ชุดคำสั่งที่ใช้เรียกงานผ่านทาง `command line `เพื่อช่วยให้จัดการงานต่างให้ง่าย รวดเร็วขึ้น ซึ่ง `laravel` นำ `Class console` ของ `symfony` มาปรับใช้ 

<a name="usage"></a>
## การใช้งาน

ในการเรียกดูคำสั่งทั้งหมดใช้คำสั่ง `list` 

	php artisan list

ทุกคำสั่งจะมีวิธีการใช้ให้เราดู  โดยเพิ่ม parameter `help` เข้าไปนะครับ

**ตัวอย่างการใช้งาน help**

	php artisan help migrate

เราสามารถเรียก พร้อมกับเปลี่ยนการตั้งค่าโดยรวมของเว็บโดยเพิ่มพารามิเตอร์ `--env` 

**ตัวอย่างนี้เราเรียกในการตั้งค่าแบบ local**

	php artisan migrate --env=local

**จะเรียกดูรุ่นของ laravel ก็ได้โดยพารามิเตอร์**  `--version`

	php artisan --version

## การสร้างคำสั่งขึ้นใช้งานเอง ##



เราสามารถสร้างคำสั่ง `artisan` ขึ้นมาใช้ โดยไฟล์จะเก็บที่โฟลเดอร์ `app/commands` ถ้าเราไม่อยากเก็บไว้ตรงนี้ก็ไปตั้งค่าที่ไฟล์  `composer.json` ได้

<a name="building-a-command"></a>
## การสร้างคำสั่ง

### เริ่มสร้างคลาส

เราจะใช้คำสั่ง `command:make` ใน `command line` เพื่อสร้าง `class ` ขึ้นมาก่อนครับ

**ตัวอย่างการใช้ command line สร้างคำสั่ง**

	php artisan command:make FooCommand

ถ้าเราอยากจะเปลี่ยนที่อยู่ให้กับไฟล์คำสั่งของเรา ก็ใช้คำสั่งนี้ไปเลยครับ

	php artisan command:make FooCommand --path="app/classes" --namespace="Classes"

### การตั้งค่าคำสั่ง

เริ่มต้นโดยการตั้ง `name` และ `description` รวมถึงส่วนประกอบอื่นของคลาส, โดยค่าเหล่านี้จะไปปรากฏตอนคำสั่ง `artisan list` 
ฟังก์ชัน  `fire` ใช้ในการเรียกฟังก์ชันต่างๆ ที่จะทำงานในคำสั่งนี้ 


### การตั้งค่าต่างๆ

 `getArguments` กับ `getOptions` เมทอด เป็นที่ๆ เราะทำการตั้งค่าจะต่างๆ ทั้ง พารามิเตอร์ที่ 1 ที่ 2 การตั้งค่าจะมีลักษณะการส่งค่าลงอาเรย์.

เมื่อเรา คำสั่งของเรามีการให้ป้อนพารามิเตอร์ ตัวของ array ต้องมีรูปแบบดังนี้

	array($name, $mode, $description, $defaultValue)

ตัวแปร `mode` เรากำหนดให้เป็นแบบต้องมี `InputArgument::REQUIRED`  หรือไม่มีก็ได้ `InputArgument::OPTIONAL`.

เมื่อเรากำหนดให้มีการใส่คำสั่งเพิ่มเติม ลักษณะอาเรย์จะเป็นแบบนี้

	array($name, $shortcut, $mode, $description, $defaultValue)

ในการกำหนด `mode` ให้เป็นได้หลายๆแบบได้เช่น

`InputOption::VALUE_REQUIRED`, `InputOption::VALUE_OPTIONAL`, `InputOption::VALUE_IS_ARRAY`, `InputOption::VALUE_NONE`.

 ตัวอย่างรูปแบบค่าที่ต้องป้อนให้เมื่อกำหนด mode เป็น `VALUE_IS_ARRAY`

	php artisan foo --option=bar --option=baz


### การเข้าถึงตัวแปร

เมื่อคำสั่งทำงาน เราก็ต้องมีตัวจัดการในการ ดึงค่าต่างๆ ในพารามิเตอร์ของการตั้งค่า ที่รับมา

**การดึงค่าจากพารามิเตอร์เฉพาะตัว**

	$value = $this->argument('name');

**การดึงค่าทั้งหมด**

	$arguments = $this->argument();

**การรับค่าจากค่าการตั้งค่าแบบเฉพาะตัว**

	$value = $this->option('name');

**การรับค่าจากค่าการตั้งค่าแบบทั้งหมด**

	$options = $this->option();

### ส่งผลการทำงาน

มีประเภทของคำสั่งที่จะแสดงออกทาง commandline 4 ประเภท คือ `info` , `comment` , `question` และ `error` ทั้ง 4 มีรูปบแบบ unicode เป็น ANSI 

**ส่งข้อมูลของคำสั่งออกทางหน้าจอ**

	$this->info('Display this on the screen');

**ส่งข้อความออกไปทางหน้าจอ**

	$this->error('Something went wrong!');

### ให้ผู้ใช้งานเลือก

เราสามารถใช้การถามคำถาม และ ยืนยัน เพื่อความรวดเร็วในการใช้งาน

**การถามคำถาม**

	$name = $this->ask('What is your name?');

**การถามคำถามและค่าที่ป้อนมาเป็นรูปแบบ**

	$password = $this->secret('What is the password?');

**ยืนยันการเลือก**

	if ($this->confirm('Do you wish to continue? [yes|no]'))
	{
		//
	}

เราสามารถกำหนดค่าเริ่มต้นของคำสั่ง `confirm` ให้เป็น`true` หรือ `false` ได้

	$this->confirm($question, true);

<a name="registering-commands"></a>
## การลงทะเบียนคำสั่ง

เมื่อการสร้างคำสั่งเสร็จสิ้น เราต้องนำไปลงทะเบียนที่ไฟล์ `app/start/artisan.php`  โดยใช้คำสั่ง `Artisan::add` เพื่อลงทะเบียน

**ตัวอย่างการใช้งาน**

	Artisan::add(new CustomCommand);

ถ้าคำสั่งเราใช้ใน [IoC container](/docs/ioc)  เราต้องใช้คำสั่ง `Artisan::resolve` เพื่อผูกคำสั่งของเราไปกับ IOC ด้วย

**ตัวอย่างการใช้งาน**

	Artisan::resolve('binding.name');

<a name="calling-other-commands"></a>
## การเรียกใช้คำสั่งอื่นร่วม

บางเวลาเราต้องการจะเรียกใช้คำสั่งอื่นๆ สามารถใช้ฟังก์ชัน `call` เรียกได้

**ตัวอย่างการใช้งาน**

	$this->call('command.name', array('argument' => 'foo', '--option' => 'bar'));

