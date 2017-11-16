# permission-role

/*==============================================================
                     Laravel role and permission
===============================================================*/
video: https://www.youtube.com/watch?v=ucG3NOKBe_8
link: https://github.com/Zizaco/entrust
link: http://itsolutionstuff.com/post/laravel-52-user-acl-roles-and-permissions-with-middleware-using-entrust-from-scratch-tutorialexample.html


### 1. In order to install Laravel 5 Entrust, just add the following to your composer.json. Then run composer update:

   "zizaco/entrust": "5.2.x-dev"

### 2. opend env
   /* change CACHE_DRIVER=file to array */
   
   CACHE_DRIVER=array

### 3. Open your config/app.php and add the following to the providers array:

   Zizaco\Entrust\EntrustServiceProvider::class,
   
### 4. In the same config/app.php and add the following to the aliases array:

  'Entrust'   => Zizaco\Entrust\EntrustFacade::class,

### 5. Run command

   php artisan vendor:publish

### 6. Open your config/auth.php and add the following to it:

   'providers' => [
    	'users' => [
        	'driver' => 'eloquent',
        	'model' => App\User::class,
        	'table' => 'users',
    		],
	  ],

### 7. If you want to use Middleware (requires Laravel 5.1 or later) you also need to add the following:

    'role' => \Zizaco\Entrust\Middleware\EntrustRole::class,
    'permission' => \Zizaco\Entrust\Middleware\EntrustPermission::class,
    'ability' => \Zizaco\Entrust\Middleware\EntrustAbility::class,

### 8. opend vender->zizaco->entrust->src->commands->MigrationCommand.php

    #### for laravel5.5
    change fire to handle

### 9. use command for generate the Entrust migration:
   php artisan entrust:migration

### 10. opend app\Providers\AppServiceProvider.php

  use Illuminate\Support\Facades\Schema;
    public function boot()
    {
        Schema::defaultStringLength(191);
    }

### 11. Use command 

    php artisan migrate

### 12. Use command

    php artisan make:auth
    php artisan make:seeder PermissionTableSeeder

### 13. opend App\database\sedds\PermissionTableSeeder.php
    <?php

	use Illuminate\Database\Seeder;
	use App\Permission;
	class PermissionTableSeeder extends Seeder
	{
	    public function run()
	    {
		$permission = [
		    [
			'name' => 'role-list',
			'parent_id' => 1,
			'display_name' => 'Display Role Listing',
			'description' => 'See only Listing Of Role'
		    ],
		    [
			'name' => 'role-create',
			'parent_id' => 1,
			'display_name' => 'Create Role',
			'description' => 'Create New Role'
		    ],
		    [
			'name' => 'role-edit',
			'parent_id' => 1,
			'display_name' => 'Edit Role',
			'description' => 'Edit Role'
		    ],
		    [
			'name' => 'role-delete',
			'parent_id' => 1,
			'display_name' => 'Delete Role',
			'description' => 'Delete Role'
		    ],
		    [
			'name' => 'post-list',
			'parent_id' => 2,
			'display_name' => 'Display post',
			'description' => 'See only Listing Of post'
		    ],
		    [
			'name' => 'post-create',
			'parent_id' => 2,
			'display_name' => 'Create post',
			'description' => 'Create New post'
		    ],
		    [
			'name' => 'post-edit',
			'parent_id' => 2,
			'display_name' => 'Edit post',
			'description' => 'Edit post'
		    ],
		    [
			'name' => 'post-delete',
			'parent_id' => 2,
			'display_name' => 'Delete post',
			'description' => 'Delete Post'
		    ]
		];
		foreach ($permission as $key => $value) {
		    Permission::create($value);
		}
	    }
	}

### 14. create Model
    ### User
       <?php
		namespace App;
		use Illuminate\Notifications\Notifiable;
		use Illuminate\Foundation\Auth\User as Authenticatable;
		use Zizaco\Entrust\Traits\EntrustUserTrait;

		class User extends Authenticatable
		{
		    use Notifiable;
		    use EntrustUserTrait; // add this trait to your user model
		    
		    protected $table = 'users';
		    protected $fillable = [
			'name','email', 'password',
		    ];

		    protected $hidden = [
			'password', 'remember_token',
		    ];
		}

     ### Role
        <?php namespace App;
		use Zizaco\Entrust\EntrustRole;
		//use Illuminate\Database\Eloquent\Model;
		class Role extends EntrustRole
		{
			protected $table = 'roles';
			protected $fillable = [
				'name',
				'display_name'
			];
		}
		
    ### Permission
    	<?php namespace App;
	use Zizaco\Entrust\EntrustPermission;
	class Permission extends EntrustPermission{}

### 14. opend app\database\seeds

    $this->call(PermissionTableSeeder::class);

### 15. run command

    php artisan db:seed --class=PermissionTableSeeder

### 16. add route app\routes\web.php

	Route::group(['prefix' => 'home','middleware' => ['auth']], function() {

		    /* role and set permission*/
		    Route::GET('role',['as'=>'role.index','uses'=>'RoleController@index','middleware' => ['permission:role-list']]);
		    Route::GET('role/create',['as'=>'role.create','uses'=>'RoleController@create','middleware' => ['permission:role-create']]);
		    Route::POST('role/store',['as'=>'role.store','uses'=>'RoleController@store','middleware' => ['permission:role-create']]);
		    Route::GET('role/edit/{id}',['as'=>'role.edit','uses'=>'RoleController@edit','middleware' => ['permission:role-edit']]);
		    Route::POST('role/update/{id}',['as'=>'role.update','uses'=>'RoleController@update','middleware' => ['permission:role-edit']]);
		    Route::GET('role/delete/{id}',['as'=>'role.delete','uses'=>'RoleController@destroy','middleware' => ['permission:role-delete']]);


		    /*users*/
		    Route::GET('user',['as'=>'user.index','uses'=>'UserController@index','middleware' => ['permission:user-list']]);
		    Route::GET('user/create',['as'=>'user.create','uses'=>'UserController@create','middleware' => ['permission:user-create']]);
		    Route::POST('user/store',['as'=>'user.store','uses'=>'UserController@store','middleware' => ['permission:user-delete']]);


		});
