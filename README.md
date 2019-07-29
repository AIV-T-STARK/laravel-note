# Laravel 

## Cài đặt
`composer create-project --prefer-dist laravel/laravel TenProject "5.*"`

## Cấu trúc thư mục

### Thư mục App
- Chứa `Controller`, `Model` và các thành phần cốt lõi khác của ứng dụng.
### Thư mục Config
- Chứa các file cấu hình cho hệ thống.
### Thư mục Database
- Giúp ta tạo CSDL.
- Có ba thành phần gồm `migrate`, `seed`, `factory`.
### Thư mục Resources
- Chứa view, các file tài nguyên chưa compile(SASS, Javascript), các file ngôn ngữ.
### Thư mục Routes
- Chứa các định nghĩa `Route`.
- Gồm các file: `web.php`, `api.php`, `console.php`, `channels.php`(từ 5.8).
### Thư mục Public
- Chứa file `index.php` là điểm đầu tiên cho tất cả các request đến ứng dụng của bạn và cấu hình autoloading. Thư mục còn chứa các tài nguyên như image, css, js.
### Thư mục Tests
- Chứa các file tests.
### file .env 
- Cài đặt liên kết tới DB cho hệ thống.

## Luồng ứng dụng cơ bản

![](./image/luong-ung-dung.jpg)

## Eloquent - Model
- Model là một lớp dữ liệu có cấu trúc giống với bảng trong CSDL, dùng để tương tác với dữ liệu trong bảng.

### Tạo Model 

```
php artisan make:model TenModel
```
### Một số thuộc tính

```php
protected $table = 'table';
protected $primaryKey = 'primaryKey';
public $timestamps = false;
...
```

### Một số phương thức

```php
App\Model::all();
App\Model::find('primaryKey');
App\Model::destroy('primaryKey');
```

- Kết hợp với query builder để tạo câu truy vấn phù hợp.
![](./image/querybuilder.png)

### Liên kết tới các model khác
```php
namespace App;

use Illuminate\Database\Eloquent\Model;

class User extends Model
{
    /**
     * Get the phone record associated with the user.
     */
    public function phone()
    {
        return $this->hasOne('App\Phone'); // ('Model', 'foreign_key', 'local_key')
    }
}
```

```php
$phone = User::find(1)->phone;
```

- One To One
Sử dụng ở model cha
```php
$this->hasOne()
```
Sử dụng ở model con
```php
$this->belongsTo()
```

- One To Many
```php
$this->hasMany()
```
```php
$this->belongsTo()
```

- Has One Through
```
users
    id - integer
    supplier_id - integer

suppliers
    id - integer

history
    id - integer
    user_id - integer
```

```php
class Supplier extends Model
{
    /**
     * Get the user's history.
     */
    public function userHistory()
    {
        return $this->hasOneThrough(
            'App\History',
            'App\User',
            'supplier_id', // Foreign key on users table...
            'user_id', // Foreign key on history table...
            'id', // Local key on suppliers table...
            'id' // Local key on users table...
        );
    }
}
```

- Has Many Through
```
countries
    id - integer
    name - string

users
    id - integer
    country_id - integer
    name - string

posts
    id - integer
    user_id - integer
    title - string
```

```php
<?php

namespace App;

use Illuminate\Database\Eloquent\Model;

class Country extends Model
{
    /**
     * Get all of the posts for the country.
     */
    public function posts()
    {
        return $this->hasManyThrough('App\Post', 'App\User');
    }
}
```

## Service provider
