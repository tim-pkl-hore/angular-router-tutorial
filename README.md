#AngularJS Router Tutorial

> Project ini tidak menggunakan java, tidak harus menggunakan STS IDE, notepad++ cukup untuk tutorial ini.

##Overview
Dalam tutorial ini, akan diperkenalkan tentang bagaimana fitur route angularjs bekerja.

##Setup
###Tools
Pada tutorial ini kita membutuhkan tools tambahan : 
- NodeJS, download dari [situs resmi nodejs](https://nodejs.org/en/download/) dan install. NodeJS hanya digunakan untuk menjalankan server http yg melayani file statis dalam tutorial ini.
- HttpServer, server http serderhana untuk development, merupakan modul nodejs. Setelah nodejs terinstall, buka command prompt dan eksekusi ```npm install http-server```.

###Project
Fitur angular route tidak berada pada module core angular (yang telah digunakan pada tutorial sebelumnya). Oleh karena itu, sebelum memulai kita harus download terlebih dahulu

File pertama yang harus dibuat adalah ```index.html```. Ini merupakan halaman utama dari contoh aplikasi (single-page) kita, di halaman ini juga akan di-render atau ditampilkan partial view sesuai dengan route yang sedang active.

```html
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>Sample AngularJS Route</title>
</head>
<body ng-app="tutorialApp">
    <div ng-view></div>
    
	<script type="text/javascript" src="/scripts/vendor/angular.min.js"></script>
    <script type="text/javascript" src="/scripts/vendor/angular-route.min.js"></script>
	<script type="text/javascript">
		// custom scripts.
		'use strict';
		angular.module('tutorialApp', ['ngRoute']);
	</script>
</body>
</html>
```

Dari potongan kode di atas, kita gunakan _directive_ yang belum kita kita ginakan sebelumnya yaitu ```ng-view```. Directive ini menunjukan dimana partial view (berdasarkan route) akan ditampilkan. Sebagai contoh, jika dalam aplikasi kita membutuhkan fitur ```create```, ```read```, ```update``` dan ```delete```, maka kemungkin kita membutuhkan beberapa tampilan yang berbeda untuk mewakili fitur-fitur tersebut. Pada saat alamat url adalah ```http://localhost/#/list``` maka partial view yang ditampilkan dalam ```<div ng-view>``` adalah ```views/partials/list.html```, saat alamat url adalah ```http://localhost/#/detail``` maka partial view yang ditampilkan dalam ```<div ng-view>``` adalah ```views/partials/details.html``` dan seterusnya.

Seperti sebelumnya, kita inisiasi module angular dengan nama ```tutorialApp```, nama ini yg digunakan dalam directive ```ng-app``` pada element ```<body>```.

```js
angular.module('tutorialApp', ['ngRoute']);
```

Berbeda dengan contoh sebelumnya, di sini kita menambahkan modul ```ngRoute``` sebagai dependency dari aplikasi kita. Oleh karena itu, kemudian kita akan bisa menggunakan komponen-komponen yg disediakan modul ini (provider $routeProvider dan service ```$route```) dalam aplikasi kita.

Selanjutnya, kita akan mendefinisikan konfigurasi route dari aplikasi kita. Setelah inisiasi module, tambahkan potongan kode berikut ini
```js
angular.module('tutorialApp')
            .config(function($routeProvider) {
                // Configure route service.
                $routeProvider.when('/', {
                    templateUrl: '/views/partials/list.html',
                    controller: 'ListCtrl'
                }).when('/register', {
                    templateUrl: '/views/partials/register.html',
                    controller: 'RegisterCtrl'
                }).when('/detail/:id', {
                    templateUrl: '/views/partials/detail.html',
                    controller: 'DetailCtrl',
                    resolve: {
                        'id': function($route) {
                            return $route.current.params.id;
                        }
                    }
                });
            });
```

Maksud dari potongan kode di atas,
- Jika url adalah ```http://localhost:8080/#/```, maka render view parsial ```/views/partials/list.html``` dengan controller ```ListCtrl``` dalam ```<div ng-view></div>```.
- Jika url adalah ```http://localhost:8080/#/register```, maka render view parsial ```/views/partials/register.html``` dengan controller ```RegisterCtrl``` dalam ```<div ng-view></div>```.
- Jika url adalah ```http://localhost:8080/#/detail/2``` (perhatikan route parameter ```:id``` bernilai 2), maka render view parsial ```/views/partials/detail.html``` dengan controller ```DetailCtrl``` dalam ```<div ng-view></div>```.

Sebagai tambahan, resolve adalah mendefiniskan object yg bisa di inject ke controller, jadi resolve ini akan dilakukan sebelum partial view (dan controller) di-render. Dalam contoh ini, kita resolve ```id``` parameter, sehingga definisi ```DetailCtrl``` bisa seperti contoh berikut ini
```
app.controller('DetailCtrl', function(id) {
    // ...
});
```

> ```angular.module('tutorialApp')``` (tanpa parameter kedua array dari modul lain), artinya kita akan menggunakan referensi modul yang sebelumnya telah kita inisiasi.
> Jadi,
> ```js
> var app = angular.module('tutorialApp', ['ngRoute']);
> app.config(function($routeProvider) {
>    // ...
> });
> ```
> 
> Sama dengan
> ```js
> angular.module('tutorialApp', ['ngRoute']);
> angular.module('tutorialApp')
>   .config(function($routeProvider) {
>    // ...
>   });
> ```
>
> Dapat juga dirangkai seperti potongan kode berikut
> 
> ```js
> angular.module('tutorialApp', ['ngRoute'])
>   .config(function($routeProvider) {
>    // ...
>   });
> ```

Bagian selanjutnya kita definisikan controller yang dibutuhkan

```js
angular.module('tutorialApp')
            .controller('ListCtrl', function($scope, $log) {
                $log.debug('Starting ListCtrl');
                
                $scope.title = "List Page";
                $scope.description = "This page show list of items";
            })
            .controller('RegisterCtrl', function($scope, $log) {
                $log.debug('Starting RegisterCtrl');
                
                $scope.title = "Register Page";
                $scope.description = "This page show registration form";
            })
            .controller('DetailCtrl', function($scope, $log, id) {
                $log.debug('Starting DetailCtrl');
                
                $scope.title = "Detail Page";
                $scope.description = "This page show detail of item with id : " + id;
            });
```

Selanjutnya kita buat parsial view (sangat sederhana, agar contoh kita tidak menjadi kompleks) dalam directory ```views/partials/```.

File ```views/partials/list.html```
```html
<h1>{{title}}</h1>
<p>{{description}}</p>
```

File ```views/partials/register.html```
```html
<h1>{{title}}</h1>
<p>{{description}}</p>
```

File ```views/partials/detail.html```
```html
<h1>{{title}}</h1>
<p>{{description}}</p>
```

Dari terminal atau _command prompt_, eksekusi command berikut ini
```sh
http-server
```

Buka browser dan ketik ```http://localhost:8080``` pada address bar

##Reference
1. [$routeProvider](https://docs.angularjs.org/api/ngRoute/provider/$routeProvider)
2. [$route](https://docs.angularjs.org/api/ngRoute/service/$route)