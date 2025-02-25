### Terminal Commands
```bash
composer create-project --prefer-dist laravel/laravel coworking-booking "12.*"
cd coworking-booking
npm install
npm install vue@next
mkdir -p app/Services app/Repositories
```

### vite.config.js
```js
import { defineConfig } from 'vite'
import laravel from 'laravel-vite-plugin'
import vue from '@vitejs/plugin-vue'
export default defineConfig({
    plugins: [
        laravel({
            input: ['resources/css/app.css', 'resources/js/app.js'],
            refresh: true,
        }),
        vue()
    ],
})
```

### resources/js/app.js
```js
import { createApp } from 'vue'
import SpacesList from './components/SpacesList.vue'
import SpaceDetails from './components/SpaceDetails.vue'
const app = createApp({})
app.component('spaces-list', SpacesList)
app.component('space-details', SpaceDetails)
app.mount('#app')
```

### resources/js/components/SpacesList.vue
```vue
<template>
  <div>
    <div v-for="space in spaces" :key="space.id">
      <h2>{{ space.name }}</h2>
      <p>{{ space.description }}</p>
      <p>{{ space.location }}</p>
      <p>{{ space.price_per_hour }}</p>
      <a :href="`/spaces/${space.id}`">Details</a>
    </div>
  </div>
</template>
<script setup>
defineProps({
  spaces: {
    type: Array,
    default: () => []
  }
})
</script>
```

### resources/js/components/SpaceDetails.vue
```vue
<template>
  <div>
    <h2>{{ space.name }}</h2>
    <p>{{ space.description }}</p>
    <p>{{ space.location }}</p>
    <p>{{ space.price_per_hour }}</p>
    <form action="/bookings" method="POST">
      <input type="hidden" name="space_id" :value="space.id" />
      <input type="date" name="booking_date" required />
      <input type="time" name="booking_time" required />
      <input type="hidden" name="_token" :value="csrfToken" />
      <button type="submit">Book Now</button>
    </form>
  </div>
</template>
<script setup>
import { ref, onMounted } from 'vue'
const props = defineProps({
  space: Object
})
const csrfToken = ref('')
onMounted(() => {
  csrfToken.value = document.querySelector('meta[name="csrf-token"]').getAttribute('content')
})
</script>
```

### resources/views/spaces/index.blade.php
```html
<!DOCTYPE html>
<html>
<head>
  <meta name="csrf-token" content="{{ csrf_token() }}">
  <title>Coworking Spaces</title>
  @vite('resources/js/app.js')
</head>
<body>
  <div id="app">
    <spaces-list :spaces="{{ json_encode($spaces) }}"></spaces-list>
  </div>
</body>
</html>
```

### resources/views/spaces/show.blade.php
```html
<!DOCTYPE html>
<html>
<head>
  <meta name="csrf-token" content="{{ csrf_token() }}">
  <title>Space Details</title>
  @vite('resources/js/app.js')
</head>
<body>
  <div id="app">
    <space-details :space="{{ json_encode($space) }}"></space-details>
  </div>
</body>
</html>
```

### database/migrations/2025_02_24_000000_create_coworking_spaces_table.php
```php
<?php
use Illuminate\Database\Migrations\Migration;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Support\Facades\Schema;
return new class extends Migration {
    public function up() {
        Schema::create('coworking_spaces', function (Blueprint $table) {
            $table->id();
            $table->string('name');
            $table->text('description');
            $table->string('location');
            $table->decimal('price_per_hour', 8, 2);
            $table->timestamps();
        });
    }
    public function down() {
        Schema::dropIfExists('coworking_spaces');
    }
};
```

### database/migrations/2025_02_24_000001_create_bookings_table.php
```php
<?php
use Illuminate\Database\Migrations\Migration;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Support\Facades\Schema;
return new class extends Migration {
    public function up() {
        Schema::create('bookings', function (Blueprint $table) {
            $table->id();
            $table->unsignedBigInteger('user_id');
            $table->unsignedBigInteger('space_id');
            $table->date('booking_date');
            $table->time('booking_time');
            $table->timestamps();
            $table->foreign('user_id')->references('id')->on('users')->onDelete('cascade');
            $table->foreign('space_id')->references('id')->on('coworking_spaces')->onDelete('cascade');
        });
    }
    public function down() {
        Schema::dropIfExists('bookings');
    }
};
```

### database/migrations/2025_02_24_000002_create_payments_table.php
```php
<?php
use Illuminate\Database\Migrations\Migration;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Support\Facades\Schema;
return new class extends Migration {
    public function up() {
        Schema::create('payments', function (Blueprint $table) {
            $table->id();
            $table->unsignedBigInteger('booking_id');
            $table->decimal('amount', 8, 2);
            $table->string('payment_method');
            $table->string('status');
            $table->timestamps();
            $table->foreign('booking_id')->references('id')->on('bookings')->onDelete('cascade');
        });
    }
    public function down() {
        Schema::dropIfExists('payments');
    }
};
```

### app/Models/CoworkingSpace.php
```php
<?php
namespace App\Models;
use Illuminate\Database\Eloquent\Factories\HasFactory;
use Illuminate\Database\Eloquent\Model;
class CoworkingSpace extends Model {
    use HasFactory;
    protected $fillable = ['name', 'description', 'location', 'price_per_hour'];
}
```

### app/Models/Booking.php
```php
<?php
namespace App\Models;
use Illuminate\Database\Eloquent\Factories\HasFactory;
use Illuminate\Database\Eloquent\Model;
class Booking extends Model {
    use HasFactory;
    protected $fillable = ['user_id', 'space_id', 'booking_date', 'booking_time'];
}
```

### app/Models/Payment.php
```php
<?php
namespace App\Models;
use Illuminate\Database\Eloquent\Factories\HasFactory;
use Illuminate\Database\Eloquent\Model;
class Payment extends Model {
    use HasFactory;
    protected $fillable = ['booking_id', 'amount', 'payment_method', 'status'];
}
```

### app/Repositories/CoworkingSpaceRepository.php
```php
<?php
namespace App\Repositories;
use App\Models\CoworkingSpace;
class CoworkingSpaceRepository {
    public function getAll() {
        return CoworkingSpace::all();
    }
    public function find($id) {
        return CoworkingSpace::findOrFail($id);
    }
}
```

### app/Repositories/BookingRepository.php
```php
<?php
namespace App\Repositories;
use App\Models\Booking;
class BookingRepository {
    public function create($data) {
        return Booking::create($data);
    }
    public function find($id) {
        return Booking::findOrFail($id);
    }
}
```

### app/Repositories/PaymentRepository.php
```php
<?php
namespace App\Repositories;
use App\Models\Payment;
class PaymentRepository {
    public function create($data) {
        return Payment::create($data);
    }
    public function find($id) {
        return Payment::findOrFail($id);
    }
}
```

### app/Services/CoworkingSpaceService.php
```php
<?php
namespace App\Services;
use App\Repositories\CoworkingSpaceRepository;
class CoworkingSpaceService {
    protected $repository;
    public function __construct(CoworkingSpaceRepository $repository) {
        $this->repository = $repository;
    }
    public function getSpaces() {
        return $this->repository->getAll();
    }
    public function getSpace($id) {
        return $this->repository->find($id);
    }
}
```

### app/Services/BookingService.php
```php
<?php
namespace App\Services;
use App\Repositories\BookingRepository;
class BookingService {
    protected $repository;
    public function __construct(BookingRepository $repository) {
        $this->repository = $repository;
    }
    public function bookSpace($data) {
        return $this->repository->create($data);
    }
}
```

### app/Services/PaymentService.php
```php
<?php
namespace App\Services;
use App\Repositories\PaymentRepository;
class PaymentService {
    protected $repository;
    public function __construct(PaymentRepository $repository) {
        $this->repository = $repository;
    }
    public function processPayment($data) {
        return $this->repository->create($data);
    }
}
```

### app/Http/Controllers/CoworkingSpaceController.php
```php
<?php
namespace App\Http\Controllers;
use App\Services\CoworkingSpaceService;
class CoworkingSpaceController extends Controller {
    protected $service;
    public function __construct(CoworkingSpaceService $service) {
        $this->service = $service;
    }
    public function index() {
        $spaces = $this->service->getSpaces();
        return view('spaces.index', compact('spaces'));
    }
    public function show($id) {
        $space = $this->service->getSpace($id);
        return view('spaces.show', compact('space'));
    }
}
```

### app/Http/Controllers/BookingController.php
```php
<?php
namespace App\Http\Controllers;
use App\Services\BookingService;
use Illuminate\Http\Request;
class BookingController extends Controller {
    protected $service;
    public function __construct(BookingService $service) {
        $this->service = $service;
    }
    public function store(Request $request) {
        $data = $request->only(['user_id', 'space_id', 'booking_date', 'booking_time']);
        $this->service->bookSpace($data);
        return redirect()->back();
    }
}
```

### app/Http/Controllers/PaymentController.php
```php
<?php
namespace App\Http\Controllers;
use App\Services\PaymentService;
use Illuminate\Http\Request;
class PaymentController extends Controller {
    protected $service;
    public function __construct(PaymentService $service) {
        $this->service = $service;
    }
    public function store(Request $request) {
        $data = $request->only(['booking_id', 'amount', 'payment_method', 'status']);
        $this->service->processPayment($data);
        return redirect()->back();
    }
}
```

### routes/web.php
```php
<?php
use App\Http\Controllers\CoworkingSpaceController;
use App\Http\Controllers\BookingController;
use App\Http\Controllers\PaymentController;
use Illuminate\Support\Facades\Route;
Route::get('/', [CoworkingSpaceController::class, 'index']);
Route::get('/spaces/{id}', [CoworkingSpaceController::class, 'show']);
Route::post('/bookings', [BookingController::class, 'store']);
Route::post('/payments', [PaymentController::class, 'store']);
```

### Final Commands
```bash
php artisan migrate
npm run dev
php artisan serve
```