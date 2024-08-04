# Blade

## Introduction

Blade adalah fitur di laravel yang digunakan untuk mempermudah dalam pembuatan tampilan halaman web HTML. Dengan blade template bisa kita bedakan lokasi logic aplikasi dengan kode tampilan. Semua blade template disimpan dalam folder resources/views.

## Variable
Di blade kita bisa menampilkan variable php menggunakan $ seperti contoh : **{{$nama}}**. Dengan demikian variable tersebut akan dirender ke template html.
Sebagai contoh kita akan buat file **hello.blade.php**.
```php
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>{{$name}}</title>
</head>

<body>
    <h1>{{$name}}</h1>
</body>

</html>
```
Kemudian untuk routingnya:
```php
Route::get('/hello', function(){
    return view('hello', [
        'name' => "alphonso"
    ]);
});
```

Sebagai unit testnya:
```php
public function testHello()
    {
        $this->get('/hello')
        ->assertSeeText('alphonso');
    }
```

## Nested View Directory
Jika file view yang kita buat sudah banyak, lebih baik disimpan di berbagai folder untuk skalabilitas yang baik. Sebagai contoh terdapat file profile.blade.php dan ingin ditaro pada folder admin. Maka untuk aksesnya maka gunakan **admin.profile**.

## Test View Tanpa Routing
Untuk melakukan test kita harus membuat routenya dlu, namun di laravel bisa juga tanpa membuat routenya untuk unit testnya. Sebagai contoh:
```php
public function testViewWithoutRoutinh()
    {
        $this->view('hello.hello', [
            'name' => 'jonathan'
        ])->assertSeeText('jonathan');
    }
```

## Comment
Blade juga mendukung untuk komentar, caranya mengunakan {{-- isi komentar --}}.

## HTML Encoding
By default ketika menampikan data menggunakan {{}} maka otomatis akan melakukan sanitasi. Jika ingin data yang ditampilkan tidak disanitasi maka  bisa menggunakan seperti ini {!! $variable !!}. Sebagai contoh:
```php
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>HTML Encoding</title>
</head>

<body>
    {!! $name !!}
</body>

</html>
```
Untuk diroutenya
```php
Route::get('/html-encoding', function (Request $request) {
    return view('html-encoding', [
        'name' => $request->input('name')
    ]);
});
```

## Disabled Blade
Ada beberapa kasus dimana kita tidak ingin code blade tereksekusi di html karena beberapa syntax yang sama dengan blade. Oleh karena itu kita bisa tambahkan @ sebelumnya untuk escape blade function atau jika banyak code yang ingin diescape maka bisa menggunakan @verbatim - @endverbatim. Sebagai contoh:
```php
<html lang="en">

<body>
    <h1>Hello @{{$name}}</h1>

    @verbatim
    <p>
        Hello {{$name}}
        Hello {{$name}}
        Hello {{$name}}
        Hello {{$name}}
    </p>
    @endverbatim
</body>

</html>
```

Untuk unit testnya:
```php
public function testDisabled()
    {
        $this->view('disabled', [
            'name' => 'dono'
        ])->assertSeeText(' Hello {{$name}}')
            ->assertDontSeeText('dono');
    }
```

## If Statement
Blade template juga mendukung percabangan if menggunakan perintah/directive @if, @elseif, @else dan @endif. Contoh:
```php
<!DOCTYPE html>
<html lang="en">

<body>
    <p>
        @if (count($hobbies) == 1)
            I just only have one hobbies.
        @elseif(count($hobbies) > 1)
            I have many hobbies
        @else
            I dont have any hobbies
        @endif
    </p>
</body>
</html>
```
Untuk unit testnya:
```php
public function testIf()
    {
        $this->view('if', [
            'hobbies' => []
        ])->assertSeeText('I dont have any hobbies');

        $this->view('if', [
            'hobbies' => ['games']
        ])->assertSeeText('I just only have one hobbies.');

        $this->view('if', [
            'hobbies' => ['football', 'soccer']
        ])->assertSeeText('I have many hobbies');
    }
```

## Unless Statement
Mirip seperti ini dengan @if namun jika nilainya false, maka isi body akan dieksekusi.
```php
<html lang="en">

<body>
    @unless ($isAdmin)
    You are not admin
    @endunless
</body>

</html>
```

## Isset and Empty
@Isset digunakan untuk mengecek apakah sebuah variable ada dan tidak bernilai null.

@empty digunakan untuk mengecek apakah sebuah variable merupakan array kosong.

Contoh 
```php
<!DOCTYPE html>
<html lang="en">
<body>
    <p>
        @isset($name)
            Welcome {{$name}} in my web.
        @endisset
    </p>
    <p>
        @empty($hobbies)
            I dont have any hobbies.
        @endempty
    </p>
</body>
</html>
```

## Env
Dalam blade template , bisa menggunakan directive @env(name) untuk mengecek env yang digunakan. Contoh:
```php
<!DOCTYPE html>
<html lang="en">
<body>
    @env('test')
        This is test environment
    @endenv
</body>
</html>
```
