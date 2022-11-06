# Login multi role dan permission dengan laravel 9

1. instal laravel
```php
laravel new namaproject
```
2. install breeze
```php
composer require laravel/breeze --dev
```
```php
php artisan install:bereeze
```
lalu migrasi
```php
php artisan migrate
```
buat akun terlebih dahulu melalui register
kemudian untuk role dan permissionnya kita akan instal package `spatie`
```php
composer require spatie/laravel-permission
```
```php
php artisan vendor:publish --provider="Spatie\Permission\PermissionServiceProvider"
```
lalu jalankan ulang
```php
php artisan migrate
```
tambahkan kode berikut pada user model laravel
```php
use Spatie\Permission\Traits\HasRoles;
```
dan tambahkan `use HasRole`

selanjutnya buat seeder untuk role dan permision
```php
php artisan make:seeder UserRolePermissionSeeder
```
pada folder `seeders/UserRolePermissionSeeder.php` tambahkan :
```php
public function run(){
    $default_user_value=[
        'email_verfied_at'=>now(),
        'password'=>bcrypt('12345'),
        'remember_token'=>str::random(10)
    ];
    DB::beginTransaction();
    try{
    $admin = User::create(array_merge([
        'email'=>'admin@gmail.com',
        'name'=>'admin',
    ],$default_user_value));

    $user = User::create(array_merge([
        'email'=>'user@gmail.com',
        'name'=>'user',
    ],$default_user_value));

    $it = User::create(array_merge([
        'email'=>'it@gmail.com',
        'name'=>'it',
    ],$default_user_value));

    $role_admin = Role::create(['name'=>'admin']);
    $role_user = Role::create(['name'=>'user']);
    $role_it = Role::create(['name'=>'it']);

    $permission = Permission::create(['name'=>'read role']);
    $permission = Permission::create(['name'=>'create role']);
    $permission = Permission::create(['name'=>'update role']);
    $permission = Permission::create(['name'=>'delete role']);

    $admin->assignRole('admin');
    $user->assignRole('user');
    $it->assignRole('it');

    DB::commit();
    }catch(\Trowable $th){
        DB::rollBack();
    }
}
```
pada folder `seeder/DatabaseSeeder.php`
```php
$this->call([UserRolePermissionSeeder::class])
```
```php
php artisan migrate:fresh --seed
```

oke sekarang cara penggunaanya
kita buat controller untuk rolenya
```php
php artisan make:controller RoleController -r
```
pada folder `routes/web.php`
cara 1:
```php
Route::resource('roles',RoleController::class);
```
cara 2:
```php
Route::controller(RoleController::class)->group(function(){
    Route::get('/roles','index');
})->middleware('can:read role');
```
atau kita juga bisa cek lewat controllernya langsung
```php
public function index(Request $request){
    if($request->user->hasRole('it')){
        return 'page it';
    }
    abort(403);
}
```
or
```php
public function index(Request $request){
    $this->authorize('read role');
    return 'page it';
}
```

karena IT bisa akses semua maka kita bisa tambahkan `givePermissionTo('read role')l;
```php
public function run(){
    $default_user_value=[
        'email_verfied_at'=>now(),
        'password'=>bcrypt('12345'),
        'remember_token'=>str::random(10)
    ];
    DB::beginTransaction();
    try{
    $admin = User::create(array_merge([
        'email'=>'admin@gmail.com',
        'name'=>'admin',
    ],$default_user_value));

    $user = User::create(array_merge([
        'email'=>'user@gmail.com',
        'name'=>'user',
    ],$default_user_value));

    $it = User::create(array_merge([
        'email'=>'it@gmail.com',
        'name'=>'it',
    ],$default_user_value));

    $role_admin = Role::create(['name'=>'admin']);
    $role_user = Role::create(['name'=>'user']);
    $role_it = Role::create(['name'=>'it']);

    $permission = Permission::create(['name'=>'read role']);
    $permission = Permission::create(['name'=>'create role']);
    $permission = Permission::create(['name'=>'update role']);
    $permission = Permission::create(['name'=>'delete role']);

    $role_it->giverPermissionTo('read role');
    $role_it->giverPermissionTo('create role');
    $role_it->giverPermissionTo('update role');
    $role_it->giverPermissionTo('delete role');

    $admin->assignRole('admin');
    $user->assignRole('user');
    $it->assignRole('it');

    DB::commit();
    }catch(\Trowable $th){
        DB::rollBack();
    }
}
```