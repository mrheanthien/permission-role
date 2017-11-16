# permission-role

/*==============================================================
                     Laravel role and permission
===============================================================*/
video: https://www.youtube.com/watch?v=ucG3NOKBe_8
link: https://github.com/Zizaco/entrust
link: http://itsolutionstuff.com/post/laravel-52-user-acl-roles-and-permissions-with-middleware-using-entrust-from-scratch-tutorialexample.html


### 1. opend composer
   pass: "zizaco/entrust": "5.2.x-dev"

### 2. opend env
   change: CACHE_DRIVER=file to CACHE_DRIVER=array

### 3. config/app.php
   -providers: Zizaco\Entrust\EntrustServiceProvider::class,
   -aliases  : 'Entrust'   => Zizaco\Entrust\EntrustFacade::class,

### 4. Run command
   php artisan vendor:publish

### 5. Open your config/auth.php
   'providers' => [
    	'users' => [
        	'driver' => 'eloquent',
        	'model' => App\User::class,
        	'table' => 'users',
    		],
	  ],

### 6. opend app/Kernel.php pass to routeMiddleware
    'role' => \Zizaco\Entrust\Middleware\EntrustRole::class,
    'permission' => \Zizaco\Entrust\Middleware\EntrustPermission::class,
    'ability' => \Zizaco\Entrust\Middleware\EntrustAbility::class,

### 7. opend vender->zizaco->entrust->src->commands->MigrationCommand.php
    change fire to handle

### 8. use command 
   php artisan entrust:migration

### 9. opend app\Providers\AppServiceProvider.php
  use Illuminate\Support\Facades\Schema;
  Schema::defaultStringLength(191);

### 10. run command 
    php artisan migrate

### 11. run command
    php artisan make:auth
    php artisan make:seeder PermissionTableSeeder

### 12. opend PermissionTableSeeder password code

### 13. create Model
    -User
    -Role
    -Permission

### 14. opend app\database\seeds
    pass: $this->call(PermissionTableSeeder::class);

### 15. run command
    php artisan db:seed --class=PermissionTableSeeder

### 16. add route
