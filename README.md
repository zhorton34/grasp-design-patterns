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


## **GRASP Patterns in Laravel Framework**

### **1. Information Expert (Expert)**

- **Definition:** Assign responsibilities to the class that has the necessary information to fulfill them.

- **Laravel Example:** **Eloquent Models**

  - **Description:**
    - Eloquent models in Laravel act as the primary information experts. They encapsulate the data and provide methods to interact with the database, such as retrieving, updating, and deleting records.
  
  - **Example:**
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

    // Usage in a controller or elsewhere
    $user = User::find(1);
    $orders = $user->orders; // User model knows how to retrieve its orders
    ```

  - **Explanation:**
    - The `User` model is responsible for accessing its related `Order` models because it possesses the necessary information about the relationship. By assigning this responsibility to the `User` model, we adhere to the Information Expert principle.

### **2. Creator**

- **Definition:** Assign the responsibility of creating an instance of class A to class B if B contains or closely uses A, has the data required to initialize A, or aggregates A.

- **Laravel Example:** **Model Factories and Relationships**

  - **Description:**
    - Laravel uses factories to create model instances for testing and seeding databases. Additionally, models often create related models through relationships.

  - **Example (Factory):**
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
                // ...
            ];
        }
    }

    // Usage in tests or seeders
    $user = User::factory()->create();
    ```

  - **Example (Relationship):**
    ```php
    // Creating an order for a user
    $order = $user->orders()->create([
        'order_number' => 'ORD123',
        'total' => 99.99,
    ]);
    ```

  - **Explanation:**
    - In both cases, the class (`User` model) that has the necessary information or closely uses the other class (`Order` model) is responsible for creating it.

### **3. Controller**

- **Definition:** Assign the responsibility of handling a system event to a class representing the overall system or a use case scenario.

- **Laravel Example:** **HTTP Controllers**

  - **Description:**
    - Laravel's controllers handle incoming HTTP requests, process user input, interact with models, and return responses.

  - **Example:**
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

  - **Explanation:**
    - The `PostController` acts as a controller by managing user input, coordinating with models (`Post`), and determining the appropriate response.

### **4. Low Coupling**

- **Definition:** Assign responsibilities to minimize dependencies between classes, reducing the impact of changes and promoting reuse.

- **Laravel Example:** **Service Container and Dependency Injection**

  - **Description:**
    - Laravel's service container allows for dependency injection, enabling classes to depend on abstractions (interfaces) rather than concrete implementations.

  - **Example:**
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

    // app/Providers/AppServiceProvider.php
    public function register()
    {
        $this->app->bind(PaymentGatewayInterface::class, StripePaymentGateway::class);
    }

    // app/Http/Controllers/OrderController.php
    namespace App\Http\Controllers;

    use App\Interfaces\PaymentGatewayInterface;

    class OrderController extends Controller
    {
        protected $paymentGateway;

        public function __construct(PaymentGatewayInterface $paymentGateway)
        {
            $this->paymentGateway = $paymentGateway;
        }

        public function store()
        {
            $this->paymentGateway->charge(100);
            // ...
        }
    }
    ```

  - **Explanation:**
    - By depending on the `PaymentGatewayInterface`, the `OrderController` is loosely coupled to the payment gateway implementation. Changes to the payment gateway do not affect the controller, adhering to the Low Coupling principle.

### **5. High Cohesion**

- **Definition:** Assign responsibilities so that related behaviors are kept together, promoting focused and manageable classes.

- **Laravel Example:** **Eloquent Models with Business Logic**

  - **Description:**
    - Models encapsulate not only data but also behaviors directly related to that data, such as custom methods and attribute accessors.

  - **Example:**
    ```php
    // app/Models/Product.php
    namespace App\Models;

    use Illuminate\Database\Eloquent\Model;

    class Product extends Model
    {
        // Attribute accessor
        public function getPriceAttribute($value)
        {
            return $value / 100; // Convert cents to dollars
        }

        // Business logic method
        public function isInStock()
        {
            return $this->stock_quantity > 0;
        }
    }
    ```

  - **Explanation:**
    - The `Product` model groups related data and behaviors, maintaining high cohesion by focusing on product-related responsibilities.

### **6. Polymorphism**

- **Definition:** Use polymorphic operations to handle alternatives based on type, promoting flexibility and extensibility.

- **Laravel Example:** **Polymorphic Relationships and Contracts**

  - **Description:**
    - Laravel's polymorphic relationships allow models to belong to more than one type of model using a single association.
    - Interfaces and contracts enable different classes to be used interchangeably if they implement the same interface.

  - **Example (Polymorphic Relationship):**
    ```php
    // app/Models/Comment.php
    namespace App\Models;

    use Illuminate\Database\Eloquent\Model;

    class Comment extends Model
    {
        public function commentable()
        {
            return $this->morphTo();
        }
    }

    // app/Models/Post.php
    namespace App\Models;

    use Illuminate\Database\Eloquent\Model;

    class Post extends Model
    {
        public function comments()
        {
            return $this->morphMany(Comment::class, 'commentable');
        }
    }

    // app/Models/Video.php
    namespace App\Models;

    use Illuminate\Database\Eloquent\Model;

    class Video extends Model
    {
        public function comments()
        {
            return $this->morphMany(Comment::class, 'commentable');
        }
    }

    // Usage
    $post = Post::find(1);
    $post->comments()->create(['content' => 'Great post!']);

    $video = Video::find(1);
    $video->comments()->create(['content' => 'Nice video!']);
    ```

  - **Explanation:**
    - The `Comment` model can be associated with both `Post` and `Video` models, demonstrating polymorphism in relationships.

### **7. Pure Fabrication**

- **Definition:** Assign responsibilities to a class that does not represent a concept in the problem domain but is created to achieve low coupling and high cohesion.

- **Laravel Example:** **Service Classes**

  - **Description:**
    - Service classes encapsulate business logic that doesn't fit within models or controllers, often used for complex operations.

  - **Example:**
    ```php
    // app/Services/ReportGenerator.php
    namespace App\Services;

    use App\Models\Order;

    class ReportGenerator
    {
        public function generateMonthlySalesReport()
        {
            $salesData = Order::whereMonth('created_at', date('m'))
                ->sum('total');

            // Generate report logic
        }
    }

    // Usage in a controller
    $reportGenerator = app(ReportGenerator::class);
    $reportGenerator->generateMonthlySalesReport();
    ```

  - **Explanation:**
    - `ReportGenerator` is a pure fabrication; it doesn't represent a domain entity but provides a cohesive set of operations related to report generation, promoting separation of concerns.

### **8. Indirection**

- **Definition:** Assign responsibilities to intermediate objects to mediate between other components, reducing coupling.

- **Laravel Example:** **Middleware and Events**

  - **Description:**
    - Middleware acts as an intermediary between HTTP requests and application logic.
    - Event listeners and subscribers decouple event producers from consumers.

  - **Example (Middleware):**
    ```php
    // app/Http/Middleware/Authenticate.php
    namespace App\Http\Middleware;

    use Closure;
    use Illuminate\Support\Facades\Auth;

    class Authenticate
    {
        public function handle($request, Closure $next)
        {
            if (Auth::check()) {
                return $next($request);
            }

            return redirect('login');
        }
    }
    ```

  - **Example (Event):**
    ```php
    // app/Events/UserRegistered.php
    namespace App\Events;

    use App\Models\User;
    use Illuminate\Queue\SerializesModels;

    class UserRegistered
    {
        use SerializesModels;

        public $user;

        public function __construct(User $user)
        {
            $this->user = $user;
        }
    }

    // app/Listeners/SendWelcomeEmail.php
    namespace App\Listeners;

    use App\Events\UserRegistered;
    use Illuminate\Contracts\Queue\ShouldQueue;
    use Illuminate\Queue\InteractsWithQueue;
    use Mail;

    class SendWelcomeEmail implements ShouldQueue
    {
        public function handle(UserRegistered $event)
        {
            Mail::to($event->user->email)->send(new WelcomeEmail($event->user));
        }
    }

    // app/Providers/EventServiceProvider.php
    protected $listen = [
        UserRegistered::class => [
            SendWelcomeEmail::class,
        ],
    ];
    ```

  - **Explanation:**
    - Middleware and event listeners act as layers of indirection, decoupling the core application logic from cross-cutting concerns like authentication and notification.

### **9. Protected Variations**

- **Definition:** Identify points of predicted variation and create a stable interface around them to protect the rest of the system from the variations.

- **Laravel Example:** **Contracts and Facades**

  - **Description:**
    - Laravel's contracts (interfaces) define stable abstractions that can be implemented by various concrete classes.
    - Facades provide a consistent interface to underlying implementations, shielding the application from changes.

  - **Example (Contract):**
    ```php
    // Illuminate\Contracts\Cache\Store

    // Usage
    use Illuminate\Contracts\Cache\Factory as CacheFactory;

    class SomeClass
    {
        protected $cache;

        public function __construct(CacheFactory $cache)
        {
            $this->cache = $cache;
        }

        public function getData()
        {
            return $this->cache->get('key');
        }
    }
    ```

  - **Example (Facade):**
    ```php
    // Using the Cache facade
    Cache::put('key', 'value', $minutes);
    $value = Cache::get('key');
    ```

  - **Explanation:**
    - By coding against interfaces (contracts) and using facades, the application is protected from changes in the underlying implementations of services like caching, logging, etc.

---

## **Summary**

By applying the **GRASP patterns**, Laravel promotes:

- **Clear Responsibility Assignment:** Ensuring classes and objects have well-defined responsibilities.
- **Low Coupling and High Cohesion:** Making the codebase easier to maintain and scale.
- **Flexibility and Extensibility:** Allowing the application to adapt to changes with minimal impact.

Understanding how these patterns manifest in Laravel helps developers write better, more maintainable code by following established design principles.

---

## **Additional Notes**

- **Separation of Concerns:** Laravel encourages separating different aspects of the application (e.g., models, views, controllers, services), aligning with the High Cohesion principle.
- **Dependency Injection:** Widely used in Laravel to inject dependencies, promoting Low Coupling.
- **Event-Driven Architecture:** Laravel's events and listeners facilitate Indirection and Protected Variations.
- **Reusable Components:** By adhering to these principles, Laravel components are reusable and interchangeable.

---

## **References**

- **Laravel Documentation:** [https://laravel.com/docs](https://laravel.com/docs)
- **GRASP Patterns Overview:** [GRASP (object-oriented design) - Wikipedia](https://en.wikipedia.org/wiki/GRASP_(object-oriented_design))
- **Design Patterns in Laravel:** [Laracasts Series - Design Patterns in PHP](https://laracasts.com/series/design-patterns-in-php)

