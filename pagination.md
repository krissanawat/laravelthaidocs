# Pagination
คือแบ่งการแสดงข้อมูลเป็นหน้าไปครับ
<a name="configuration"></a>
## การตั้งค่า

การตั้งค่าการของอยู่ที่ `app/config/view.php` ตัวแปรชื่อ `pagination` ในการแบ่งหน้า ฟังก์ชัน `pagination::slider` ใช้ในการสร้างเลขหน้า `pagination::simple` ใช้สร้างปุ่ม  "previous" และ "next"  

<a name="usage"></a>
## การใช้งาน

การใช้งานมีอยู่หลายรูปแบบ เเต่ที่ง่ายที่สุดใช้เมทอด `paginate`บน query builder หรือ Eloquent model.

**ตัวอย่างโดยใช้ query builder**

	$users = DB::table('users')->paginate(15);

You may also paginate [Eloquent](/docs/eloquent) models:

**ตัวอย่างโดยใช้ Eloquent Model**

	$users = User::where('votes', '>', 100)->paginate(15);

ในตัวอย่างเรากำหนดจำนวนข้มูลต่อหน้าได้ ส่วนการแสดงผลบน view เราจะใช้ฟังก์ชัน `links` 

	<div class="container">
		<?php foreach ($users as $user): ?>
			<?php echo $user->name; ?>
		<?php endforeach; ?>
	</div>

	<?php echo $users->links(); ?>

เพียงแค่นี้ก็จะได้การแบ่งหน้าละครับ.

เราสามารถจัดการๆ แบ่งหน้าได้โดยฟังก์ชันต่อไปนี้ครับ:

- `getCurrentPage`
- `getLastPage`
- `getPerPage`
- `getTotal`
- `getFrom`
- `getTo`

บางครั้งเราอยากสร้างเองเพราะอาจจะมีข้อมูลที่ต้องผ่านการคำนวนหลายขั้น ก็ใช้เมทอดนี้เลยครับ `Paginator::make` 

**ตัวอย่าง**

	$paginator = Paginator::make($items, $totalItems, $perPage);

<a name="appending-to-pagination-links"></a>
## Appending To Pagination Links

เราสามารทำการเรียงลำดับการแสดงผลได้โดยใช้เมทอด `appends` เหมือนในตัวอย่าง

	<?php echo $users->appends(array('sort' => 'votes'))->links(); ?>

ลิ้งที่ออกมาหน้าตาจะเป็นแบบนี้

	http://example.com/something?page=2&sort=votes