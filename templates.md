# Templates

<a name="controller-layouts"></a>
## Controller Layouts

เราใช้ตัวแปร `layout` เพื่อกำหนดเลเอาท์หลักให้กับ controller ครับ

**ตัวอย่างการใช้งาน**

	class UserController extends BaseController {

		/**
		 * The layout that should be used for responses.
		 */
		protected $layout = 'layouts.master';

		/**
		 * ส่งวิวเฉพาะไปให้เลเอาท์หลัก
		 */
		public function showProfile()
		{
			$this->layout->content = View::make('user.profile');
		}

	}

<a name="blade-templating"></a>
## Blade Templating

Blade template คือคลาสที่ใช้ในการเขียน html ขึ้นมาด้วย php ทำให้เราสามารถสร้าง html ที่มีโครงสร้างซับซ้อนได้ การจะใช้นั้นต้องตั้งชื่อไฟล์เป็น `ชื่อ view.blade.php` 

**ตัวอย่างที่ยังใช้ html อยู่**

	<!-- Stored in app/views/layouts/master.blade.php -->

	<html>
		<body>
			@section('sidebar')
				This is the master sidebar.
			@show

			<div class="container">
				@yield('content')
			</div>
		</body>
	</html>

**ใช้ blade ในการสร้างเลเอาท์ทั้งหมด**

	@extends('layouts.master')

	@section('sidebar')
		@parent

		<p>This is appended to the master sidebar.</p>
	@stop

	@section('content')
		<p>This is my body content.</p>
	@stop

 `extend` ใช้ในการดึงค่าจากเลเอาท์อื่นมาใช้ `@parent` ทำให้เราสามารถใช้ view อื่นแทรกเข้ามาได้ `@section` ก็ใช้งานโดยการแทรก html จากไฟล์อื่นเข้าไป

<a name="other-blade-control-structures"></a>
## การใช้งานฟังก์ชัน php ใน blade

**การแสดงข้อมูล การแทรกข้อมูล**

	Hello, {{ $name }}.

	The current UNIX timestamp is {{ time() }}.

ทีนี้เราก์ใช้ syntax แบบสั้นๆ ในการแสดงผลจาก server ได้เเล้ว

	Hello, {{{ $name }}}.


**การใช้ if**

	@if (count($records) === 1)
		I have one record!
	@elseif (count($records) > 1)
		I have multiple records!
	@else
		I don't have any records!
	@endif

	@unless (Auth::check())
		You are not signed in.
	@endunless

**การใช้ Loops**

	@for ($i = 0; $i < 10; $i++)
		The current value is {{ $i }}
	@endfor

	@foreach ($users as $user)
		<p>This is user {{ $user->id }}</p>
	@endforeach

	@while (true)
		<p>I'm looping forever.</p>
	@endwhile

**การแทรก Views**

	@include('view.name')

**การแสดงภาษา**

	@lang('language.line')

	@choice('language.line', 1);

**การทำคอมเม้น**

	{{-- This comment will not be in the rendered HTML --}}
