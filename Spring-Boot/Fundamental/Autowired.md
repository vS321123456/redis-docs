Understanding @Autowired in Spring Framework
📌 What is @Autowired?
@Autowired is an annotation provided by the Spring Framework used for dependency injection. It allows Spring to automatically resolve and inject collaborating beans into your class.

🧩 Why Use Dependency Injection?
Promotes loose coupling
Improves testability
Enhances modularity
Simplifies configuration and management
🔧 Ways to Use @Autowired
1. Field Injection

Injects dependency directly into the field.
Simple and concise.
Not recommended for testing and immutability.
2. Constructor Injection

Injects dependencies via constructor.
Promotes immutability and better testability.
Recommended approach.
3. Setter Injection

Injects dependencies via setter methods.
Useful when dependencies are optional.
## 🆚 Constructor Injection vs Field Injection

| Feature                    | Constructor Injection       | Field Injection (`@Autowired`) |
|----------------------------|-----------------------------|--------------------------------|
| **Visibility of dependencies** | High (explicit)              | Low (implicit)                 |
| **Testability**               | Easy                         | Harder                         |
| **Immutability**              | Supports final fields         | No                             |
| **Required dependencies**     | Enforced                     | Optional                       |
| **Readability**               | Clear                        | Less clear                     |

✅ Best Practice
Use constructor injection whenever possible. It’s cleaner, safer, and more testable.

