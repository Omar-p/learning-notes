- let:
  - Context object:   it
  - Return value:   lambda result
  - The let function is frequently used for null safety calls. For null safety, use the safe call operator(?.) with ‘let'. It only runs the block with a non-null value.
- apply:-
  - Context object:  this
  - Return value:   context object
  - “Apply these to the object,” as the name suggests. It can be used to operate on receiver object members, primarily to initialise them.
- with:-
  - Context object:  this
  - Return value:   lambda result
- run:-
  - Context object:  this
  - Return value:   lambda result
  - The ‘run' function is a combination of the ‘let' and ‘with' functions. When the object lambda involves both initialization and computation of the return value, this is the method to use. We can use run to make null safety calls as well as other calculations.
- also:-
  - Context object:  it
  - Return value:   context object
  - It's used when we need to do additional operations after the object members have been initialised.
---

- Nothing type can be used as a return type for a function that always throws an exception. When you call such a function, 
  the compiler uses the information that the execution doesn't continue beyond the function.
- using infix function allows you to call a function with a single parameter without using the dot and parentheses.
  - mapOf(1 to "one", 2 to "two") // ex: to
- passing anonymous function to a function instead of a lambda:
  - anonymous function can have multiple return statements.
- for nested class to be able to access properties of the outer class, the nested class should be marked as `inner`.
- the class cannot not have more than one companion object.
  - to get direct access to a companion object member from java, you need to annotate the companion object field with `@JvmStatic`.
- to hide the primary constructor, you can use private constructor.
  - ex: `class DontCreateMe private constructor()`
- Delegate properties:
  - class Controller(repo: Repository) : Repository by repo
    - now, you can call the methods of the Repository class directly on the Controller class.

---
### **Kotlin OOP Summary**
**For Interviews & First-Time Study**

---

#### **1. Classes**
- **Definition**:
  - Basic unit of OOP. Use `class` keyword.
  - Can be empty (useful in specific cases).
  - **Syntax**:
    ```kotlin
    class ClassName { /* Properties/Methods */ }
    ```

- **Properties**:
  - **State** of the class.
  - Must be initialized (e.g., `var name: String = ""`).
  - `val`: Read-only (immutable).
  - `var`: Read-write (mutable).
  - Can be nullable (`var name: String? = null`).

- **Methods**:
  - **Behavior** of the class. Use `fun`.
  - Access class properties directly.
  - **Example**:
    ```kotlin
    fun feed(food: String) { println("$name ate $food") }
    ```

---

#### **2. Instances**
- **Creation**: No `new` keyword.
  ```kotlin
  val person = Person()
  ```
- **Access Properties/Methods**:
  ```kotlin
  person.name = "Kevin"  
  person.feed("Cake")
  ```

---

#### **3. Constructors & Initialization**
- **Default Constructor**: Automatically generated.
- **Primary Constructor**: Defined in class header (covered in next module).

---

#### **4. Getters/Setters & Backing Fields**
- **Custom Accessors**:
  ```kotlin
  var name: String = ""
    get() = field.uppercase()
    set(value) { 
      if (value.isNotEmpty()) field = value 
    }
  ```
  - `field`: Backing field for the property.
- **Validation**: Use setters to validate input (e.g., non-empty name).

---

#### **5. Visibility Modifiers**
- **Default**: `public`.
- **Encapsulation**: Use `private` to restrict access.
  ```kotlin
  private var calories: Int = 0  // Only accessible within the class
  private fun updateCalories() { ... }
  ```

---

#### **6. Extension Functions**
- **Add Methods to Existing Classes** (e.g., `String`):
  ```kotlin
  fun String.removeWhitespace(): String = this.replace(" ", "")
  ```
  - **Usage**:
    ```kotlin
    val text = "Hello World".removeWhitespace() // "HelloWorld"
    ```
  - **Note**: No access to private members of the extended class.

---

#### **7. Key Takeaways**
- **Kotlin vs Java**:
  - No `new` keyword.
  - Classes can be in the same file.
  - Default `public` visibility.
- **Encapsulation**: Use `private` for controlled access.
- **Backing Fields**: Simplify property logic.
- **Extension Functions**: Enhance readability without modifying source classes.

---

**Next Topic**: Constructors (Parameterized, Initialization Blocks).

👉 **Quick Revision Tips**:
- Practice defining classes with `val`/`var` properties.
- Experiment with custom getters/setters for validation.
- Try creating extension functions for common tasks (e.g., formatting).
---

---

### **Kotlin Generics & Advanced Types Summary**


#### **1. Generics Basics**
- **Purpose**: Create flexible, reusable classes/functions that work with any type while ensuring type safety.
- **Syntax**:
  ```kotlin
  class Box<T>(val item: T)  // Generic class
  fun <T> printItem(item: T)  // Generic function
  ```
- **Usage**:
  - Collections (e.g., `MutableList<String>`, `List<Person>`).
  - Type safety enforced at compile time (e.g., can’t add `Int` to `List<String>`).

---

#### **2. Variance**
- **Invariance (Default)**:
  - `MutableList<String>` ≠ `MutableList<Any>`.
  - No subtype relationship even if `String` is a subtype of `Any`.

- **Covariance (`out`)**:
  - **Producer** of elements (read-only).
  - Subtype allowed: `List<String>` is a subtype of `List<Any>`.
  ```kotlin
  interface Producer<out T> { fun produce(): T }
  ```

- **Contravariance (`in`)**:
  - **Consumer** of elements (write-only).
  - Supertype allowed: `Consumer<Any>` is a subtype of `Consumer<String>`.
  ```kotlin
  interface Consumer<in T> { fun consume(item: T) }
  ```

---

#### **3. Combining Variance**
- If a class both produces and consumes a type, split into separate interfaces:
  ```kotlin
  class ProducerConsumer<T> : Producer<out T>, Consumer<in T>
  ```

---

#### **4. Star Projections (`*`)**
- **Use Case**: When the exact type doesn’t matter (e.g., checking size).
  ```kotlin
  fun compareSizes(list1: List<*>, list2: List<*>) = list1.size == list2.size
  ```
- **Constraints**:
  - `List<*>` is equivalent to `List<out Any?>`.
  - Can access elements as `Any?` but cannot add elements.

---

#### **5. Reified Generics**
- **Problem**: Type erasure removes generic type info at runtime.
- **Solution**: Use `inline` functions with `reified` to retain type info:
  ```kotlin
  inline fun <reified T> checkType(item: Any) = item is T
  ```
- **Example**:
  ```kotlin
  val isString = checkType<String>("Hello")  // true
  ```

---

#### **6. Key Concepts**
- **Type Constraints**: Use `where` clauses to restrict generics:
  ```kotlin
  class SQLResult<T>(val data: T) where T : List<Any>
  ```
- **Nullable Generics**:
  ```kotlin
  class Box<T : Any>  // Non-nullable
  class Box<T : Any?> // Nullable
  ```

---

#### **7. Practical Examples**
- **Generic Wrapper Class**:
  ```kotlin
  data class Envelope<T>(val data: T?, val message: String?)
  val personEnvelope = Envelope(Person("Alice"), "Success")
  ```
- **Covariant Producer**:
  ```kotlin
  class StringProducer : Producer<String> { override fun produce() = "Data" }
  val producer: Producer<Any> = StringProducer()  // Allowed due to `out`
  ```

---

**Tips**:
- Explain covariance/contravariance with real-world examples (e.g., `List` vs. `MutableList`).
- Demonstrate reified generics for runtime type checks.
- Use star projections when type specifics are irrelevant.

👉 **Quick Revision**:
- **`out`** = Producer (subtypes allowed).
- **`in`** = Consumer (supertypes allowed).
- **`*`** = "Don’t care" type.
- **`reified`** = Retain type at runtime in `inline` functions.

**Next Topic**: Advanced Kotlin features like delegates, coroutines, or DSLs.
