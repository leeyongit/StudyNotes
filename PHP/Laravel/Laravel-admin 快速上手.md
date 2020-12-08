#  Laravel-admin 快速上手

具体代码

```php
php artisan make:model Movie -m
php artisan migrate
composer require fzaninotto/faker
php artisan make:seeder MovieTableSeeder
php artisan db:seed --class=MovieTableSeeder
php artisan admin:make MovieController --model=App\\Movie
$router->resource('movies', MovieController::class);
```

1. 建立模型，并创建 Migrations：

```sh
$ php artisan make:model Movie -m
```

2. 在 Migrations，增加一个字段：name

```php
$table->string('name', 50)->unique();
```

3. 运行 Migrations，创建对应数据库：

```sh
php artisan migrate
```

4. 有了数据表，就需要往表里插入 fake 数据，用于测试

```sh
$ composer require fzaninotto/faker
```

5. 建立 Seeder

```
$ php artisan make:seeder MovieTableSeeder
```

在该类中，建立 100 条数据：

```php
$faker = Faker\Factory::create();

   for($i = 0; $i < 100; $i++) {
      	App\Movie::create([
       'name' => $faker->name
     ]);

   }
```

运行：

```sh
php artisan db:seed --class=MovieTableSeeder
```

6. 建立 Controller

```sh
php artisan admin:make MovieController --model=App\\Movie
```

7. route 添加

```php
$router->resource('movies', MovieController::class);
```

8. 加入到 admin 的 menu 中

