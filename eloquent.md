# Eloquent ORM



<a name="introduction"></a>
## ทำความรู้จักก่อน

 Eloquent ORM คือการที่เราจำลองตารางเป็นคลาสแล้ว เรียกใช้งานเป็นชื่อของตาราง นั้นเลยทำให้เข้าใจการพัฒนารวดเร็วขึ้น เข้าใจง่ายขึ้น ที่เรียกว่า Eloquent เพราะตัวมันมีความเรียบง่าย 

<a name="basic-usage"></a>
## การใช้งานเบื้องต้น

เริ่มต้นด้วยการสร้าง moedel ไว้ที่โฟลเดอร์ `app/models` 

**ตัวอย่างการสร้าง model**

	class User extends Eloquent {}

ถ้าคลาสนี้จะไม่ใช้ตารางตามชื่อ model เราก็สามารถใช้ตัวแปร `table` ในการกำหนดชื่อตารางที่เราจะใช้ เหมือนในตัวอย่าง

	class User extends Eloquent {

		protected $table = 'my_users';

	}

> **หมายเหตุ:** Eloquent จะถือว่าคอลัมน์ `id` เป็นคีย์หลักเสมอ เราสามารถใช้ตัวแปร `primaryKey` เพื่อกำหนดคีย์หลักได้เอง และเช่นเดียวกัน เราสามารถใช้ตัวแปร `connection`เพื่อกำหนดฐานข้อมูลที่เราจะใช้ใน model นี้

ถ้าในตารางของเรามีคอลัมน์ที่ชื่อ `updated_at` กับ `created_at` จะถูกใช้ในการเก็บเวลาที่ข้อมูลในแถวนี้ถูกเพิ่มหรือแก้ไข. ถ้าเราไม่ต้องการเพียงแค่ตั้งค่าตัวแปร `$timestamps` ให้เป็น `false` 

**การคิวรี่โดยใช้ eoloquent**

   ค้นหาข้อมูลทั้งหมดจากตาราง user

	$users = User::all(); //

**ค้นหาตามเงื่อนไข**

ค้นหาโดยค่า id เท่ากับ 1

	$user = User::find(1); //

แสดงค่าในคอลัมน์ออกมา

	var_dump($user->name); // 

> **Note:** ทุกคำสั่งที่ใช้ใน [query builder](/docs/queries) สามารถใช้กับ eloquent ได้เช่นกัน

**การคิวรี่แล้วส่งต่อ**

บางเวลาเมื่อค้นหาเเล้วไม่เจอเราต้องการให้เกิดหน้าแสดงข้อผิดพลาดขึ้นมา สามารถใช้ฟังก์ชัน `findOrFail` เหมือนในตัวอย่างเลยครับ ถ้าค้นไม่เจอเราจะส่งไปหน้า 404 ทันที

	$model = User::findOrFail(1);

	$model = User::where('votes', '>', 100)->firstOrFail();

อยากจะสร้างการแสดงข้อผิดพลาดโดยที่เรากำหนดเองก็สามารถทำตามตัวอย่างเลยครับ สมมุติเราจะสร้างฟังก์ชัน `ModelNotFoundException`เราก็เรียกตัวคลาสหลักเข้ามาก่อน

	use Illuminate\Database\Eloquent\ModelNotFoundException;

	App::error(function(ModelNotFoundException $e)
	{
		return Response::make('Not Found', 404);
	});

**ตัวอย่างการคิวรี่แบบหลายเงื่อนไข**

	$users = User::where('votes', '>', 100)->take(10)->get();

	foreach ($users as $user)
	{
		var_dump($user->name);
	}


**Eloquent Aggregates**

	$count = User::where('votes', '>', 100)->count();

ถ้าเราอยากเขียนคำสั่งคิวรี่ ขึ้นมาใช้เองก็ต้องใช้ฟังก์ชัน `whereRaw`

	$users = User::whereRaw('age > ? and votes = 100', array(25))->get();

<a name="mass-assignment"></a>
## การส่งค่าอาเรย์ลงฐานข้อมูล

เราสามารถส่ง ค่าจำนวนมากอย่างเช่น อาเรย์ ลงฐานข้อมูลได้ง่ายๆ แต่ต้องใช้ตัวแปร<br/> 
 `fillable` เพื่อกำหนดว่าคอลัมน์ไหนที่สามารถใส่อาเรย์ได้ <br/>
`guarded` เพื่อกำหนดว่าคอลัมน์ไหนใส่อาเรย์ลงไปไม่ได้ 

**ตัวอย่างการกำหนดค่า fillable**

	class User extends Eloquent {

		protected $fillable = array('first_name', 'last_name', 'email');

	}


**ตัวอย่างการตั้งค่าตัวแปร guard**

	class User extends Eloquent {

		protected $guarded = array('id', 'password');

	}

ต้วอย่างคอลัมน์ `id` and `password` เราจะไม่อนุญาติให้ทำการใส่ค่าที่มาในรูปแบบอาเรย์ลงไป

**การป้องกันไม่ให้ทำการเพิ่มข้อมูลเป็นอาเรย์**

	protected $guarded = array('*');

<a name="insert-update-delete"></a>
## เพิ่ม, ลบ, แก้ไข


**ตัวอย่างการแก้ไขข้อมูลแบบไม่ใช้ namespace**

	$user = new User;

	$user->name = 'John';

	$user->save();

> **หมายเหตุ:** โดยเริ่มต้น laravel จะทำการเพิ่มค่าคีย์หลักให้อัตโนมัติ ถ้าเราไม่ต้องการก็ตั้งค่าตัวแปร `incrementing`ใน model ให้เป็น `false`.

เราสามารถใช้คำสั่ง create เพื่อสร้างข้อมูลใหม่ได้ แต่ก่อนหน้านั้นต้องกำหนดตัวแปร `fillable` หรือ `guarded` ไม่งั้นเพิ่มไม่ได้ติด error 


**การสร้างข้อมูลใหม่**

	$user = User::create(array('name' => 'John'));


**ตัวอย่างการแก้ไขข้อมูล**

	$user = User::find(1);

	$user->email = 'john@foo.com';

	$user->save();

บางเวลาเราต้องบันทึกค่าในตารางที่อ้างอิงกัน เราจะใช้คำสั่ง `push` 

**บันทึกค่าพร้อมกับบันทึกลงตารางที่มีการเชื่อมกันอยู่**

	$user->push();


**ตัวอย่างการลบข้อมูล**

	$user = User::find(1);

	$user->delete();

**ลบโดยกำหนด id เป็นเงื่อนไข**

	User::destroy(1);

	User::destroy(1, 2, 3);

ตัวอย่างการลบแบบมีเงื่อนไข:

	$affectedRows = User::where('votes', '>', 100)->delete();

ถ้าเราต้องการแก้ไขเฉพาะคอลัมน์ที่ใช้บันทึกเวลา เราจะใช้คำสั่ง `touch` 

**ตัวอย่างการใช้งาน**

	$user->touch();

<a name="soft-deleting"></a>
## การกำหนดว่าข้อมูลนี้ถูกลบเเล้ว
เราสร้างตัวแปร `$softdelete` เพื่อบอก model ว่าไม่ต้องลบจริง เหมือนกับเราเอาไปเก็บไว้ในถังขยะก่อน ยังไม่ได้เอาไปเผาทิ้งจริงๆ

	class User extends Eloquent {

		protected $softDelete = true;

	}
แล้วก็เพิ่มคอลัมน์ `deleted_at` ลงในตาราง เพื่อกำหนดว่าข้อมูลแถวนี้ถูกลบแล้วหรือยัง

เมื่อเราเรียกคำสั่ง `delete` กับ model นี้คอลัมน์ `deleted_at` จะถูกเพิ่มค่าให้เป็นวันเวลาที่เราลบ เมื่อเราค้นหาข้อมูลโดยใช้ model นี้ข้อมูลแถวที่เราทำการ
ลบจะไม่ถูกดึงขึ้นมา 

**ตัวอย่างการค้นหา โดยรวมแถวที่ถูกตั้งค่าว่าลบแล้ว**

	$users = User::withTrashed()->where('account_id', 1)->get();

**ตัวอย่างการค้นหา โดยค้นหาเฉพาะแถวที่ถูกตั้งค่าว่าลบแล้ว**

	$users = User::onlyTrashed()->where('account_id', 1)->get();

ถ้าต้องการยกเลิกการลบ ใช้คำสั่ง `restore` ได้เลยครับ

	$user->restore();

หรือจะเรียกคืนเฉพาะแถวก็ตามตัวอย่างนี้เลย

	User::withTrashed()->where('account_id', 1)->restore();

ฟังก์ชัน `restore` สามารถใช้กับความสัมพันธ์ได้ด้วย

	$user->posts()->restore();

ถ้าต้องการลบข้อมูลจริงๆ ก็ใช้คำสั่ง `forceDelete`

	$user->forceDelete();

คำสัง `forceDelete` ก็สามารถใช้กับความสัมพันธ์ก็ได้

	$user->posts()->forceDelete();

ฟังก์ชัน `trashed` ใช้ในการตรวจว่าโมเดลนี้มีการตั้งค่า softdelete ไว้ไหม

	if ($user->trashed())
	{
		//
	}

<a name="timestamps"></a>
## Timestamps การบันทึกเวลา

โดยค่าเริ่มต้น laravel ใช้คอลัมน์ `created_at` และ `updated_at` ในตารางของเราโดยอัตโนมัติ.

**ตัวอย่างการยกเลิกการเก็บเวลาในการจัดการข้อมูล**

	class User extends Eloquent {

		protected $table = 'users';

		public $timestamps = false;

	}

ฟังก์ชัน `freshTimestamp`ใช้ในการกำหนดรูปแบบวันเวลาที่เราจะเก็บ

**ตัวอย่างการใช้งาน**

	class User extends Eloquent {

		public function freshTimestamp()
		{
			return time();
		}

	}

<a name="query-scopes"></a>
## Query Scopes

เราใช้คำนำหน้าฟังก์ชันว่า `scope` เพื่อทำการสร้างฟังก์ชันที่ใช้คิวรี่แบบเฉพาะของเราเอง:

**ตัวอย่างการใช้งาน scope**

	class User extends Eloquent {

		public function scopePopular($query)
		{
			return $query->where('votes', '>', 100);
		}

	}

**การใช้งานคิวรี่ที่มาจาการใช้คำสั่ง scope**

	$users = User::popular()->orderBy('created_at')->get();

<a name="relationships"></a>
## ความสัมพันธ์

การจัดการความสัมพันธ์ตารางใน laravel มี 4 รูปแบบ

- 1 ต่อ 1
- 1 ต่อ กลุ่ม
- กลุ่ม ต่อ กลุ่ม
- ความสัมพันธ์แบบซับซ้อน

<a name="one-to-one"></a>
### 1 ต่อ 1

ตัวอย่างความสัมพันธ์แบบ 1 ต่อ 1  ผู้ใช้งานมีโทรศัพท์ได้เเค่เครื่องเดียว

**ตัวอย่างความสัมพันธ์แบบ 1 ต่อ 1**

	class User extends Eloquent {

		public function phone()
		{
			return $this->hasOne('Phone');
		}

	}

เราใช้ฟังก์ชันเป็นตัวกำหนดตารางที่เราจะเชื่อมด้วย ตัวอย่างข้างล่างการค้นหาโทรศัพท์ของผู้ใช้งานที่มี id เท่ากับ 1

	$phone = User::find(1)->phone;

ถ้าเขียนเป็น php ธรรมดาก็จะได้เเบบนี้ครับ

	select * from users where id = 1

	select * from phones where user_id = 1

โดยค่าเริ่มต้นเเล้ว Eloquent จะใช้คอลัมน์ `user_id` ในตาราง `Phone`  เป็น คีย์เชื่อม ถ้าเราไม่เอา จะเอาชื่อที่เราตั้งเองก็ใช้ตัวแปร `hasOne` เป็นตัวแก้ดังตัวอย่าง

	return $this->hasOne('Phone', 'custom_key');

ในการเชื่อมโมเดลสิ่งที่สำคัญคือการตั้งค่าความสัมพันธ์ให้ตรงกันใน `Phone` model เราก็จะใช้ฟังก์ชัน `belongsTo` ในการเชื่อมกลับไปยัง `User`

**ตัวอย่างการเชื่อมกลับไปยัง User Model**

	class Phone extends Eloquent {

		public function user()
		{
			return $this->belongsTo('User');
		}

	}

เหมือนกับข้างบนครับ เราไม่เอา `user_id` เป็นคีย์เชื่อมก็ต้องกำหนดด้วย ตามตัวอย่าง

	class Phone extends Eloquent {

		public function user()
		{
			return $this->belongsTo('User', 'custom_key');
		}

	}

<a name="one-to-many"></a>
### 1 ต่อ กลุ่ม

ความสัมพันธ์แบบ 1 ต่อ กลุ่ม มีตัวอย่างคือ 1 โพสมีได้หลาย ความคิดเห็น
 ตัวอย่างการใช้ hasMany

	class Post extends Eloquent {

		public function comments()
		{
			return $this->hasMany('Comment');
		}

	}

ตัวอย่างการค้นหาตารางที่เชื่อมกันอยู่

	$comments = Post::find(1)->comments;

ตัวอย่างการค้นหาแบบหลายเงื่อนไขครับ ดังตัวอย่าง เราจะค้นหาความคิดเห็นที่มี title ชื่อ foo โดยเอาค่าเเรกที่เจอก่อน

	$comments = Post::find(1)->comments()->where('title', '=', 'foo')->first();

อีกครั้ง อย่าลืมเชื่อมกลับไปยังตารางที่เชื่อมมานะครับ

**ตัวอย่างอีกครั้ง**

	class Comment extends Eloquent {

		public function post()
		{
			return $this->belongsTo('Post');
		}

	}

<a name="many-to-many"></a>
### กลุ่ม ต่อ กลุ่ม

กลุ่มต่อกลุ่ม จะเป็นความสัมพันธ์ที่ยุ่งยากพอสมควรเลยครับ เรามีตัวอย่างคือ ผู้ใช้งานมีสิทธิการใช้งานได้หลายสิทธิ์ ทั้งเรียกดู,ลบ,แก้ไข,เพิ่ม แล้วแต่ละสิทธิ์ก็ถูกใช้ได้ในหลายผู้ใช้งาน เราต้องมี 3 ตาราง `users`, `roles`, กับ `role_user`. ตาราง `role_user`จะเก็บ `user_id` กับ `role_id`  เพื่อบอกว่า ผู้ใช้งานคนนี้มีสิทธิทำอะไรได้บ้าง

laravel ใช้ฟังก์ชัน `belongsToMany` ในการเชื่อมความสัมพันธ์:

	class User extends Eloquent {

		public function roles()
		{
			return $this->belongsToMany('Role');
		}

	}

ตอนนี้เราสามารถตรวจได้เเล้วว่าผู้ใช้งานหมายเลข 1 มีสิทธิทำอะไรได้บ้าง

	$roles = User::find(1)->roles;

ถ้าเราต้องการใช้ชื่อตาราง ตามใจเราก็สามารถทำได้โดยการ ส่งพารามิเตอร์ไป ดังตัวอย่างครับ

	return $this->belongsToMany('Role', 'userroles');

หรือจะเปลี่ยนไปจนถึงชื่อคอลัมน์เลยก็ได้ แต่ต้องส่งชื่อ คอลัมน์ที่เราตั้งเองไปบอก model ด้วย

	return $this->belongsToMany('Role', 'userroles', 'user_id', 'foo_id');

อย่าลืมเชื่อมความสัมพันธ์กลับมาด้วยนะครับ

	class Role extends Eloquent {

		public function users()
		{
			return $this->belongsToMany('User');
		}

	}

<a name="polymorphic-relations"></a>
### ความสัมพันธ์ที่ยุ่งยากมากขึ้น
เพื่อช่วยให้เข้าใจได้ง่ายขึ้นจะมีตัวอย่างมาให้ดูกันครับ
**ตัวอย่างโครงสร้างตาราง**

	staff
		id - integer
		name - string

	orders
		id - integer
		price - integer

	photos
		id - integer
		path - string
		imageable_id - integer
		imageable_type - string

มันพิเศษตรงที่คอลัมน์ `imageable_id` กับ `imageable_type` ของตาราง `photos` ที่เราจะใช้เก็บคีย์ที่ใช้เชื่อมตาราง photo เข้ากับตาราง staff หรือ order  ใช้เก็บคีย์เชื่อมร่วมกันได้  โดยใช้คอลัมน์ เมื่อเรากำหนดคีย์เชื่อมและชื่อของตารางที่เชื่อมไป ORM จะทำการตรวจสอบโดยใช้คอลัมน์ที่ `imageable_type` ในการหาว่าคีย์นี้เป็นของตารางไหน โดยเราต้องตั้งชื่อฟังก์ชันว่า `imageable` เราต้องประกาศ model แบบนี้ครับ

	class Photo extends Eloquent {

		public function imageable()
		{
			return $this->morphTo();
		}

	}

	class Staff extends Eloquent {

		public function photos()
		{
			return $this->morphMany('Photo', 'imageable');
		}

	}

	class Order extends Eloquent {

		public function photos()
		{
			return $this->morphMany('Photo', 'imageable');
		}

	}

ตอนนี้เราสามารถใช้ id ของ staff หรือ order มาค้นหารูปภาพได้

**ตัวอย่าง**

	$staff = Staff::find(1);

	foreach ($staff->photos as $photo)
	{
		//
	}

ความพิเศษจริง ๆอยู่ที่เมื่อเราใช้ `Photo` model ในการค้นครับ

**ตัวอย่าง**

	$photo = Photo::find(1);

	$imageable = $photo->imageable;

ความสัมพันธ์ที่ชื่อ `imageable` บน model `Photo` จะส่งข้อมูลของทั้ง `Staff` และ `Order` หรือตารางใดตารางหนึ่ง ขึ้นอยู่กับค่าที่เราใช้ค้นหาจะไปตรงกับ model ไหน



<a name="querying-relations"></a>
## การคิวรี่โดยใช้ความสัมพันธ์เป็นเงื่อนไข

เราสามารถจำกัดผลการค้นหาด้วยฟังก์ชัน `has` 

**ค้นหาโดยจำกัดเฉพาะความสัมพันธ์**

	$posts = Post::has('comments')->get(); // เลือกข้อมูลของตาราง post โดยเอาเฉพาะที่มี comment

You may also specify an operator and a count:

	$posts = Post::has('comments', '>=', 3)->get(); // เลือก post ที่มี comment มากกว่าหรือเท่ากับ 3 

<a name="dynamic-properties"></a>
### การค้นหาแบบยืดหยุ่น

Eloquent ทำให้เราสามารถค้นหาแบบต่อเนื่องโดย พยายามให้เราจำกัดขอบเขตการค้นหาให้ได้ลึกลงที่สุดเพื่อให้ได้เฉพาะข้อมูลที่ต้องการจริงๆ และพลิกแพลงรูปแบบของฟังก์ชันได้มากมาย ดังตัวอย่าง

	class Phone extends Eloquent {

		public function user()
		{
			return $this->belongsTo('User');
		}

	}

	$phone = Phone::find(1);

แทนที่เราจะทำแบบข้างบน ซึ่งจะทำให้เราได้ค่าที่ไม่ต้องการออกมามาก เราก็เปลี่ยนมาใช้แบบข้างล่าง เราสามารถเข้าถึง อีเมล์ ของผู้ใช้งาน คนแรกได้เลย

	echo $phone->user()->first()->email;

หรือจะให้สั้นได้อีก ก็ทำตามนี้เลยครับ

	echo $phone->user->email;

<a name="eager-loading"></a>
## Eager Loading

Eager loading มีเพื่อแก้ปัญหาการคิวรี่แบบ N + 1 ตัวอย่างคือ, ผู้เเต่งหนึ่งคนสามารถแต่งหนังสือได้หลายๆ เล่ม ความสัมพันธ์จะออกมาแบบนี้

	class Book extends Eloquent {

		public function author()
		{
			return $this->belongsTo('Author');
		}

	}

แล้วการคิวรี้ที่มีปัญหาก็ประมาณนี้

	foreach (Book::all() as $book)
	{
		echo $book->author->name;
	}

1 คิวรี้ จะทำการดึงค่าหนังสือทั้งหมดจากตาราง, แล้วการคิวรี่ครั้งต่อไปก็จะทำเหมือนกัน. ถ้ามีหนังสือ 25 เล่ม,จะมีการคิวรี่ถึง 26 ครั้ง คือเราใช้ข้อมูลทั้งหมดของตารางหนังสือไปค้นหาผูแต่ง 1  ผู้แต่งก็จะไปดึงหนังสือทั้งหมดของเขาออกมา

    select * from books

	select * from authors where id = ?

นึกถึงเรามีข้อมูลเริ่มต้น 1000 แถว ปัญหานี้ส่วนใหญ่จะเกิดขึ้นกับความสัมพันธ์แบบ  hasMany เพราะเราต้องนำทั้งหมดไปค้นหาต่อแล้วแต่ละแถวจะได้ผลลัพท์ออกมาหลายๆ แถวจำนวนผลการค้นหาที่มหาศาลจะทำให้การคิวรี่ช้ามากแต่ ซึงถ้าข้อมูลตั้งต้นยังมากกว่านี้ลงไปแต่งคิวรี่เองโดยใช้ Fluent Query Builder แต่ถ้ายังจะใช้ Eloquent ก็ยัง
โชคดีที่ laravel มีฟังก์ชัน `with` ใช้ในการทำให้เร็วขึ้น

	foreach (Book::with('author')->get() as $book)
	{
		echo $book->author->name;
	}

sql ที่เกิดขึ้นจะมีหน้าตาแบบนี้ครับ เริ่มจากค้นหาหนังสือทั้หมดก่อนแล้ว ค่อยเอา id ที่ได้ไปค้นต่อในตาราง authors เราเปลี่ยนไปใช้ in แทน

	select * from books

	select * from authors where id in (1, 2, 3, 4, 5, ...)

จะทำให้เว็บของเราโหลดเร็วขึ้นอย่างมากเลยครับ

ตัวอย่าง การใช้ eager loading ในกรณีตารางมีการเชื่อมกับอีกหลายตาราง

	$books = Book::with('author', 'publisher')->get();

จะใช้การทำ eager load กับคอลัมน์อื่นได้

	$books = Book::with('author.contacts')->get();

In the example above, the `author` relationship will be eager loaded, and the author's `contacts` relation will also be loaded.

### Eager Load Constraints
บางเวลาเราต้องการเฉพาะบางคอลัม์จาการทำ eager loading แล้วใส่เงื่อนไขเข้าไปอีก สามารถกำหนดได้ดัังนี้ครับ:

	$users = User::with(array('posts' => function($query)
	{
		$query->where('title', 'like', '%first%');
	}))->get();



### Lazy Eager Loading

เราสามารถใช้การทำ eager loading ไปยังตารางที่เชื่อมกันได้เหมือนในตัวอย่างครับ
เราเข้าไปค้นต่อไปในตาราง publisher ที่เชื่อมกับตาราง author อีก

	$books = Book::all();

	$books->load('author', 'publisher');

<a name="inserting-related-models"></a>
## การบันทึกข้อมูลแบบมีความสัมพันธ์

สมมุตอเราจะเพิ่มความคิดเห็นลงบทความนี้แล้วเราก็ต้องนำ id ของบทความที่เราโพสความคิดเห็นใส่ไปมาใส่ในความคิดเห็นด้วย 

**ตัวอย่างการเก็บข้อมูลที่ต้องมีความสัมพันธ์**

	$comment = new Comment(array('message' => 'A new comment.'));

	$post = Post::find(1);

	$comment = $post->comments()->save($comment);

ในตัวอย่างคอลัมน์`post_id` จะถูกใส่ค่าให้อัติโนมัติ

### Associating Models (Belongs To)

เมื่อเราจะทำการแก้ไขข้อมูลที่มีความสัมพันธ์แบบ กลุ่มต่อหนึ่ง เราต้องใช้ฟังก์ชัน `associate` ในการเพิ่มค่าคีย์เชื่อมไปยังตารางที่มีความสัมพันธ์อยู่ด้วย

	$account = Account::find(10);

	$user->account()->associate($account);

	$user->save();

### การเพิมข้อมูลแบบกลุ่มต่อกลุ่ม (Many To Many)

ตัวอย่างคือเราจะทำการเพิ่มความสามารถให้ผู้เราต้อง laravel มีฟังก์ชัน `attach` มาให้ใช้เเล้ว

**การเพิ่มข้อมูลแบบกลุ่มต่อกลุ่ม**

	$user = User::find(1);

	$user->roles()->attach(1);

ตัวอย่างข้างล่าง เราจะทำการเพิ่มข้อมูลไปยังตารางที่ใช้เชื่อม สามารถส่งเป็นอาเรย์ก็ได้:

	$user->roles()->attach(1, array('expires' => $expires));

เมื่อเพิ่มแล้วก็ลบได้ ฟังก์ชัน `detach` ใช้ลบค่าในตารางที่ใช้เชื่อม

	$user->roles()->detach(1);

เราสามารถใช้ฟังก์ชัน `sync`เมทอด เพื่อการเพิ่มค่าไปยังตารางที่เชื่อมอยู่ด้วยได้ ในขณะที่เพิ่มลงในตารางหลัก

**ตัวอย่างการใช้ sync กับความสัมพันธ์แบบกลุ่มต่อกลุ่ม**

	$user->roles()->sync(array(1, 2, 3));


**Aตัวอย่างการใช้ sync กับตารางกลาง**

	$user->roles()->sync(array(1 => array('expires' => true)));

การใช้เมทอด `save` เพื่อทำการเพิ่มข้อมูลงตารางที่เชื่อมกันอยู่

	$role = new Role(array('name' => 'Editor'));

	User::find(1)->roles()->save($role);

ในตัวอย่างเราสร้าง `Role` model แล้วแนบไปกับ User model. แล้วยังสามารถแนบอาเรย์เข้าไปได้อีก

	User::find(1)->roles()->save($role, array('expires' => $expires));

<a name="touching-parent-timestamps"></a>
## การแก้ไขคอลัมน์ที่เก็บเวลาในตารางที้่่เชื่อมด้วย

ตัวอย่าง เมื่อเราแก้ไขข้อมูลในตาราง Comment เราต้องการแก้ไขข้อมูลในตาราง Post ในแถวที่เชื่อมกันด้วย laravel เตรียมฟังก์ชัน `touch` มาให้เเล้ว วิธีการใช้งานในตัวอย่างเลยครับ

	class Comment extends Eloquent {

		protected $touches = array('post');

		public function post()
		{
			return $this->belongsTo('Post');
		}

	}

ตอนนี้ถ้าเราทำการแก้ไขข้อมูลในตาราง `Comment`, คอลัมน์  `updated_at` ข้อมูลในตาราง `Post`ที่เชื่อมด้วยก็จะถูกแก้ไขด้วย


<a name="working-with-pivot-table"></a>
## การจัดการตารางที่ใช้เชื่อม

laravel เตรียมฟังก์ชัน `pivot` มาให้เราใช้ในการจัดการข้อมูลของตารางที่ใช้เชื่อมตรงกลางระหว่างสองตาราง ดังตัวอย่างเลยครับ

	$user = User::find(1);

	foreach ($user->roles as $role)
	{
		echo $role->pivot->created_at;
	}

คลาส `Role` model จะดึงค่าออกมาจากตารางกลาง โดยใช้ฟังก์ชัน`pivot` โดยอัติโนมัติ

โดยค่าเริ่มต้นแล้วค่าที่ได้จาก ตารางที่เป็นตัวเชื่อมจะมีค่าเดียวที่ใช้อ้างอิงไปยัง อีกตารางคือ id เท่านั้น แต่ถ้าเราต้องการเพิ่ม ก็ต้องเพิ่มไปตอนที่กำหนดความสัมพันธ์แบบในตัวอย่าง

	return $this->belongsToMany('Role')->withPivot('foo', 'bar');

ตอนนี้ตัวแปร `foo` กับ `bar` จะถูกใช้กับฟังก์ชัน `pivot` ในการจัดการตาราง `Role`

และถ้าเราต้องการ คอลัมน์ `created_at` กับ `updated_at` เพื่อใช้กำหนดเวลา laravel มีฟังก์ชัน `withTimestamps` ซึ่งเราต้องกำหนดตอนประกาศความสัมพันธ์ครับ

	return $this->belongsToMany('Role')->withTimestamps();


ต่อมาถ้าเราต้องการลบข้อมูลในตารางในตารางกลาง เพื่อความถูกต้องของข้อมูลเราจะใช้ฟังก์ชัน `detach`นะครับ

**ตัวอย่างการใช้งาน**

	User::find(1)->roles()->detach();

เราจะทำการแก้ไขไม่ให้ผู้ใช้งานหมายเลข 1 มีสิทธิในการทำอะไรเลย

<a name="collections"></a>
## Collections

ข้อมูลที่เป็นผลลัพท์ของการค้นหานั้นจะกลับออกมาเป็น อาเรย์ eloquent อำนวนความสะดวกให้เราโดยมีฟังกชัน `contains` ให้ในการตรวจสอบข้อมูล

**ตรวจว่าในผลลัพท์ที่ได้มามีข้อมูลที่มีคีย์หลัก เป็น 2 ไหม**

	$roles = User::find(1)->roles;

	if ($roles->contains(2))
	{
		//
	}

เพื่อความสบายของเรายิ่งขึ้นไปอีกเมื่อค้นเสร็จก็เอาเฉพาะค่า role แปลงเป็นอาเรย์ หรือ json เสร็จสรรพเลย 

	$roles = User::find(1)->roles->toArray();

	$roles = User::find(1)->roles->toJson();

แทนที่จะใช้การทำ foreach แบบปกติเหมือนเดิม eloquent มีฟังก์ชัน each กับ filter มาให้ 
**การใช้งาน each และ filter**

	$roles = $user->roles->each(function($role)
	       {

	       });

	$roles = $user->roles->filter(function($role)
	           {

	           });

**เพิ่มการ Callback**

	$roles = User::find(1)->roles;

	$roles->each(function($role)
	{
		//
	});

**เรียงลำดับค่าที่อยู่ในอาเรย์ด้วยฟังก์ชัน sortby**

	$roles = $roles->sortBy(function($role)
	{
		return $role->created_at;
	});

บางครั้งเราต้องการเปลียนแปลงค่าทั้งออปเจคเลย eloquent ก็มีฟังก์ชัน `newCollection` ให้ใช้ในการเขียนทับ

**ตัวอย่างการใช้งาน**

	class User extends Eloquent {

		public function newCollection(array $models = array())
		{
			return new CustomCollection($models);
		}

	}

<a name="accessors-and-mutators"></a>
## Accessors & Mutators

ถ้ายังไม่เข้าใจว่าสองฟังก์ชันนี้มันคืออะไร ทำอะไรได้บ้าง แนะนำเข้าไปอ่านที่ผมสรุปไว้ก่อนใน [บล็อก](http://taqmaninw.com/accesor-กับ-mutator-จะใช้ยังไงใน-php/) ครับ
บางเวลาเราต้องการ จัดรูปแบบข้อมูลให้อยู่ในรูปแบบที่เราต้องการ ก่อนจะบันทึกหรือดึงมาใช้ eloquent เตรียมฟังก์ชัน `getFooAttribute` แต่การตั้งชื่อฟังก์ชันคำเริ่มต้นของคำที่เป็นชื่อของคอลัมน์ ต้องขึ้นต้นด้วยตัวพิมพ์ใหญ่ ในกรณีที่มีเครื่องหมาย _ มาคั่น คำหลังจากนั้นก็ต้องขึ้นต้นด้วยตัวพิมพ์ใหญ่ครับ

**ตัวอย่าง**

	class User extends Eloquent {

		public function getFirstNameAttribute($value)
		{
			return ucfirst($value);
		}

	}

ในตัวอย่างเราทำการสร้าง accessor ของคอลัมน์ `first_name` ทีนี้ค่าที่เราส่งเข้าฟังก์ชันนี้ก็จะถูกส่งไปเก็บถูกที่ละครับ

การสร้างฟังก์ชัน Mutator ก็คล้ายๆ กัน

**ตัวอย่าง**

	class User extends Eloquent {

		public function setFirstNameAttribute($value)
		{
			$this->attributes['first_name'] = strtolower($value);
		}

	}

<a name="date-mutators"></a>
## Date Mutators

โดยค่าเริ่มต้นเเล้ว Eloquent จะทำการตั้งค่าให้คอลัมน์ `created_at`, `updated_at`, และ `deleted_at` ตามค่าเบื้องต้นของ php.ini อยู่เเล้วนะครับ 

แต่ถ้าเราต้องการแก้ไขหรือเรียกใช้งานแบบไม่อยากเข้าไปยุ่งตรงๆ ก็สามารถใช้ฟังก์ชัน  `getDates` แบบในตัวอย่างเลยครับ

	public function getDates()
	{
		return array('created_at');
	}

ข้างในเราสามารถจัดการก่อนเอาไปใช้ได้เลย และ laravel ก็มีคลาสจัดการ วันเวลาที่มีฟังก์ชันหลากหลายมากอย่าง `Carbon` มาให้ใช้ด้วย แต่ต้องไปประกาศชื่อย่อในไฟล์ app.php ก่อนนะครับ โดยค่าเริ่มต้นเเล้วไม่มี 


<a name="model-events"></a>
## Model Events

Eloquent เตรียมฟังก์ชันที่คอยดักจับเหตุการณ์ต่างๆ มาให้เราดังนี้ครับ `creating`, `created`, `updating`, `updated`, `saving`, `saved`, `deleting`, `deleted`. แต่ถ้าค่าที่ส่งกลับมาเป็น `false` เหตุการณ์ `creating`, `updating`, หรือ `saving` จะถูกยกเลิก

**การยกเลิกการแก้ไขข้อมูล**

	User::creating(function($user)
	{
		if ( ! $user->isValid()) return false;
	});

การที่เราจะสร้างฟังก์ชัน์ในการจัดการเหตุการณ์ของ model ต้องประกาศฟังชัน `boot` ก่อนนะครับ

**การประกาศฟังก์ชัน boot**

	class User extends Eloquent {

		public static function boot()
		{
			parent::boot();

			// Setup event bindings...
		}

	}

<a name="model-observers"></a>
## Model Observers

Eloquent มีคลาสชื่อ Observer ในการสร้างฟังก์ชันที่ใช้จัดการเหตุการณ์  ฟังก์ชัน`creating`, `updating`, `saving` ก็ตั้งตามเหตุการณ์ที่จะให้ฟังก์ชันนั้นจัดการครับ ตัวอย่าง

	class UserObserver {

		public function saving($model)
		{
			//
		}

		public function saved($model)
		{
			//
		}

	}

แล้วเราก็ต้องประกาศโดยใช้ฟังชัน `observe` แบบตัวอย่าง

	User::observe(new UserObserver);

<a name="converting-to-arrays-or-json"></a>
## การแปลงค่าเป็น Arrays หรือ JSON


**การแปลงผลลัพทที่ค้นมาให้กลายเป็น array**

	$user = User::with('roles')->first();

	return $user->toArray();

	return User::all()->toArray();


**การแปลงผลลัพธ์ให้กลายเป็น json**

	return User::find(1)->toJson();


**การใช้งาน Eloquent จากใน route เลย**

	Route::get('users', function()
	{
		return User::all();
	});

บางเวลาาเราไม่อยากให้บางคอลัมน์ถุกเรียกไปพร้อมกับ `toJson` หรือ `toArray` เราก็ใช้ตัวแปร `hidden` ในการนั้น

**ต้วอย่างการใช้งาน**

	class User extends Eloquent {

		protected $hidden = array('password');

	}
