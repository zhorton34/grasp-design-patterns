# GRASP Design patterns in Laravel framework

Here's a structured table of contents for the GRASP patterns in Laravel framework, complete with relative links to each section:

---

## **GRASP Patterns in Laravel Framework**

| Pattern                             | Description                                                                                       |
|-------------------------------------|---------------------------------------------------------------------------------------------------|
| [1. Information Expert](#1-information-expert-expert) | Assign responsibilities to the class that has the necessary information to fulfill them.       |
| [2. Creator](#2-creator)           | Assign the responsibility of creating an instance of class A to class B if B contains or closely uses A. |
| [3. Controller](#3-controller)     | Assign the responsibility of handling a system event to a class representing the overall system or a use case scenario. |
| [4. Low Coupling](#4-low-coupling) | Assign responsibilities to minimize dependencies between classes, reducing the impact of changes and promoting reuse. |
| [5. High Cohesion](#5-high-cohesion) | Assign responsibilities so that related behaviors are kept together, promoting focused and manageable classes. |
| [6. Polymorphism](#6-polymorphism) | Assign responsibility for behavior to the appropriate object based on its type.                |
| [7. Pure Fabrication](#7-pure-fabrication) | Assign responsibilities to classes that are not part of the problem domain to achieve low coupling and high cohesion. |
| [8. Indirection](#8-indirection)   | Assign responsibilities to intermediate objects to mediate between other components.            |
| [9. Protected Variations](#9-protected-variations) | Design to protect elements from variations in other elements by encapsulating the concept that varies. |

---

### **1. Information Expert (Expert)**

- **Definition:** Assign responsibilities to the class that has the necessary information to fulfill them.

- **Laravel Example:** **Eloquent Models**

  ```php
  // app/Models/User.php
  namespace App\Models;

  use Illuminate\Database\Eloquent\Model;

  class User extends Model
  {
      public function orders()
      {
          return $this->hasMany(Order::class);
      }
  }
  ```

### **2. Creator**

- **Definition:** Assign the responsibility of creating an instance of class A to class B if B contains or closely uses A.

- **Laravel Example:** **Model Factories and Relationships**

  ```php
  // database/factories/UserFactory.php
  use App\Models\User;
  use Illuminate\Database\Eloquent\Factories\Factory;

  class UserFactory extends Factory
  {
      protected $model = User::class;

      public function definition()
      {
          return [
              'name' => $this->faker->name,
              'email' => $this->faker->unique()->safeEmail,
          ];
      }
  }
  ```

### **3. Controller**

- **Definition:** Assign the responsibility of handling a system event to a class representing the overall system or a use case scenario.

- **Laravel Example:** **HTTP Controllers**

  ```php
  // app/Http/Controllers/PostController.php
  namespace App\Http\Controllers;

  use App\Models\Post;
  use Illuminate\Http\Request;

  class PostController extends Controller
  {
      public function store(Request $request)
      {
          $validated = $request->validate([
              'title' => 'required',
              'content' => 'required',
          ]);

          $post = Post::create($validated);
          return redirect()->route('posts.show', $post);
      }
  }
  ```

### **4. Low Coupling**

- **Definition:** Assign responsibilities to minimize dependencies between classes.

- **Laravel Example:** **Service Container and Dependency Injection**

  ```php
  // app/Interfaces/PaymentGatewayInterface.php
  namespace App\Interfaces;

  interface PaymentGatewayInterface
  {
      public function charge($amount);
  }

  // app/Services/StripePaymentGateway.php
  namespace App\Services;

  use App\Interfaces\PaymentGatewayInterface;

  class StripePaymentGateway implements PaymentGatewayInterface
  {
      public function charge($amount)
      {
          // Stripe charging logic
      }
  }
  ```

### **5. High Cohesion**

- **Definition:** Assign responsibilities so that related behaviors are kept together.

- **Laravel Example:** **Eloquent Models with Business Logic**

  ```php
  // app/Models/Product.php
  namespace App\Models;

  use Illuminate\Database\Eloquent\Model;

  class Product extends Model
  {
      // Business logic related to Product
      public function calculateDiscount($percentage)
      {
          return $this->price - ($this->price * ($percentage / 100));
      }
  }
  ```

### **6. Polymorphism**

- **Definition:** Assign responsibility for behavior to the appropriate object based on its type.

- **Laravel Example:** **Contracts and Implementations**

  ```php
  // app/Services/PaymentGateway.php
  namespace App\Services;

  use App\Interfaces\PaymentGatewayInterface;

  class PaypalPaymentGateway implements PaymentGatewayInterface
  {
      public function charge($amount)
      {
          // Paypal charging logic
      }
  }
  ```

### **7. Pure Fabrication**

- **Definition:** Assign responsibilities to classes that are not part of the problem domain.

- **Laravel Example:** **Repositories**

  ```php
  // app/Repositories/UserRepository.php
  namespace App\Repositories;

  use App\Models\User;

  class UserRepository
  {
      public function findByEmail($email)
      {
          return User::where('email', $email)->first();
      }
  }
  ```

### **8. Indirection**

- **Definition:** Assign responsibilities to intermediate objects to mediate between other components.

- **Laravel Example:** **Middleware**

  ```php
  // app/Http/Middleware/CheckUserIsAdmin.php
  namespace App\Http\Middleware;

  use Closure;

  class CheckUserIsAdmin
  {
      public function handle($request, Closure $next)
      {
          if (!auth()->user()->isAdmin()) {
              return redirect('home');
          }
          return $next($request);
      }
  }
  ```

### **9. Protected Variations**

- **Definition:** Design to protect elements from variations in other elements.

- **Laravel Example:** **Service Providers**

  ```php
  // app/Providers/AppServiceProvider.php
  namespace App\Providers;

  use Illuminate\Support\ServiceProvider;
  use App\Interfaces\PaymentGatewayInterface;
  use App\Services\StripePaymentGateway;

  class AppServiceProvider extends ServiceProvider
  {
      public function register()
      {
          $this->app->bind(PaymentGatewayInterface::class, StripePaymentGateway::class);
      }
  }
  ```
