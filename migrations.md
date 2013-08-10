# Migrations & Seeding

<a name="introduction"></a>
## คำอธิบายเบื้องต้น

Migrations คือการเก็บประวัติสร้างจุดเซฟของฐานข้อมูล. ทำให้เราสามารถเพิ่มลบตาราง โดยย้อนกลับได้หากไม่ถูกใจ ส่วนการเขียนตารางต้องไปดูเรื่อง [Schema Builder](/docs/schema) การทำ migration จะเป็นการควบคุมการทำงานของ schema.

<a name="creating-migrations"></a>
## สร้าง Migrations

เริ่มด้วยการรันคำสัง `migrate:make` บน commandline:

**การสร้างตารางโดยใช้ commandline**

	php artisan migrate:make create_users_table

ไฟล์ migration ถูกเก็บไว้ที่โฟลเดอร์ `app/database/migrations` แต่ละไฟล์จะมีวันกำกับชื่อ ด้วยเพื่อให้ระบบรู้ลำดับการสร้างไฟล์

เราสามารถกำหนดที่อยู่ของไฟล์ได้โดยพารามิเตอร์ `--path` เหมือนตัวอย่างข้างล่างครับ

	php artisan migrate:make foo --path=app/migrations

พารามิเตอร์ `--table` และ `--create` ใช้ในการสร้าง ตารางทั้งคู่เลยครับ

	php artisan migrate:make create_users_table --table=users --create

<a name="running-migrations"></a>
## Running Migrations

**การสั่งให้คำสั่ง migration ทำงาน**

	php artisan migrate

**กำหนดที่อยู่ของไฟล์ที่จะรัน**

	php artisan migrate --path=app/foo/migrations

**สั่งรันเฉพาะตรง package**

	php artisan migrate --package=vendor/package

> **หมายเหตุ:** ถ้าเราเจอ `error Class not found`ให้รันคำสั่ง `composer update`.

<a name="rolling-back-migrations"></a>
## Rolling Back Migrations

**การย้อนกลับการทำงานครั้งล่าสุด**

	php artisan migrate:rollback

**การย้อนกลับทั้งหมด**

	php artisan migrate:reset

**การย้อนกลับแล้วทำงานใหม่อีกรอบ**

	php artisan migrate:refresh

	php artisan migrate:refresh --seed

<a name="database-seeding"></a>
## Database Seeding

Laravel เตรียมการฟังก์ชันที่ช่วยในการป้อนข้อมูลจำลอง ที่อาจจะใช้ทดสอบการทำงานของเว็บไว้ในโฟลเดอร์ `app/database/seeds`. ชื่อคลาสเราก็ตั้งชื่อตามตารางและรูปแบบให้เป็นไปตามแบบนี้ครับ `UserTableSeeder`, โดยค่าเริ่มต้นเเล้วจะเป็น `DatabaseSeeder` โดยใช้ฟังก์ชัน `call` เพื่อรันคลาสอื่นๆ
ทำให้เราลำดับการทำงานได้ 

**ตัวอย่าง**

	class DatabaseSeeder extends Seeder {

		public function run()
		{
			$this->call('UserTableSeeder');

			$this->command->info('User table seeded!');
		}

	}

	class UserTableSeeder extends Seeder {

		public function run()
		{
			DB::table('users')->delete();

			User::create(array('email' => 'foo@bar.com'));
		}

	}

การทำงานก็ใช้คำสั่ง `db:seed` command บน Artisan CLI:

	php artisan db:seed

ทำการย้อนกลับการทำงานด้วย

	php artisan migrate:refresh --seed
