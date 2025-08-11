# Swift Interview Questions

## Table of Contents
1. [Basic Swift Concepts](#basic-swift-concepts)
2. [Object-Oriented Programming](#object-oriented-programming)
3. [Protocol-Oriented Programming](#protocol-oriented-programming)
4. [Memory Management](#memory-management)
5. [Closures and Functions](#closures-and-functions)
6. [Error Handling](#error-handling)
7. [Generics](#generics)
8. [Advanced Topics](#advanced-topics)

---

## Basic Swift Concepts

### Q1: What is Swift and what are its key features?

**Answer:**
Swift is a modern programming language developed by Apple for iOS, macOS, watchOS, and tvOS development.

**Key Features:**
- **Type Safety** - Prevents type-related errors at compile time
- **Memory Safety** - Automatic memory management with ARC
- **Fast Performance** - Compiled language with optimization
- **Interoperability** - Works seamlessly with Objective-C
- **Modern Syntax** - Clean, expressive, and easy to read

```swift
// Example showing Swift's clean syntax
let numbers = [1, 2, 3, 4, 5]
let doubled = numbers.map { $0 * 2 }
print(doubled) // [2, 4, 6, 8, 10]
```

### Q2: What's the difference between `var` and `let`?

**Answer:**
- **`var`** - Creates a mutable variable (can be changed)
- **`let`** - Creates an immutable constant (cannot be changed)

```swift
var mutableValue = 10
mutableValue = 20 // ✅ Allowed

let immutableValue = 10
// immutableValue = 20 // ❌ Compile error

// However, contents of reference types can still be modified
let mutableArray = [1, 2, 3]
mutableArray.append(4) // ✅ Allowed - array contents can change
```

### Q3: Explain optionals in Swift

**Answer:**
Optionals represent values that might be `nil`. They're Swift's way of handling the absence of a value safely.

```swift
// Declaration
var optionalString: String? = "Hello"
var nilString: String? = nil

// Unwrapping methods

// 1. Force unwrapping (use carefully!)
let forced = optionalString! // Crashes if nil

// 2. Optional binding (safe)
if let unwrapped = optionalString {
    print(unwrapped) // "Hello"
}

// 3. Guard statement
guard let unwrapped = optionalString else {
    return
}
print(unwrapped)

// 4. Nil coalescing operator
let result = optionalString ?? "Default"

// 5. Optional chaining
let length = optionalString?.count
```

### Q4: What are value types vs reference types?

**Answer:**
- **Value Types** - Data is copied when assigned (struct, enum, tuple)
- **Reference Types** - Reference to memory location is shared (class, closure)

```swift
// Value Type Example (Struct)
struct Point {
    var x: Int
    var y: Int
}

var point1 = Point(x: 1, y: 2)
var point2 = point1 // Copies the data
point2.x = 10

print(point1.x) // 1 (unchanged)
print(point2.x) // 10

// Reference Type Example (Class)
class Person {
    var name: String
    init(name: String) { self.name = name }
}

let person1 = Person(name: "John")
let person2 = person1 // Shares reference
person2.name = "Jane"

print(person1.name) // "Jane" (both changed)
print(person2.name) // "Jane"
```

### Q5: Explain type inference in Swift

**Answer:**
Swift can automatically determine the type of a variable based on its initial value.

```swift
// Explicit type annotation
let explicitString: String = "Hello"

// Type inference
let inferredString = "Hello" // Swift infers String type
let inferredInt = 42         // Swift infers Int type
let inferredDouble = 3.14    // Swift infers Double type

// Complex type inference
let numbers = [1, 2, 3]      // Array<Int>
let mixed = ["name": "John"] // Dictionary<String, String>

// Function return type inference
func multiply(a: Int, b: Int) -> Int {
    return a * b // Return type inferred as Int
}
```

---

## Object-Oriented Programming

### Q6: What's the difference between class and struct?

**Answer:**

| Feature | Class | Struct |
|---------|--------|--------|
| **Type** | Reference type | Value type |
| **Inheritance** | Supports inheritance | No inheritance |
| **Mutability** | Can be mutable with `let` | Immutable with `let` |
| **Identity** | Has identity (===) | No identity comparison |
| **Memory** | Heap allocation | Stack allocation |
| **Thread Safety** | Not thread-safe by default | Thread-safe (copied) |

```swift
// Class Example
class Vehicle {
    var speed: Int = 0
    
    func accelerate() {
        speed += 10
    }
}

class Car: Vehicle { // Inheritance allowed
    var doors: Int = 4
}

// Struct Example
struct Point {
    var x: Int
    var y: Int
    
    mutating func moveBy(x deltaX: Int, y deltaY: Int) {
        x += deltaX
        y += deltaY
    }
}

// Usage
let vehicleRef = Vehicle()
vehicleRef.speed = 50 // ✅ Allowed even with let

let point = Point(x: 0, y: 0)
// point.x = 10 // ❌ Error - struct is immutable with let
```

### Q7: Explain inheritance in Swift

**Answer:**
Inheritance allows a class to inherit properties and methods from another class.

```swift
// Base class
class Animal {
    var name: String
    
    init(name: String) {
        self.name = name
    }
    
    func makeSound() {
        print("Some generic animal sound")
    }
}

// Derived class
class Dog: Animal {
    var breed: String
    
    init(name: String, breed: String) {
        self.breed = breed
        super.init(name: name) // Call parent initializer
    }
    
    override func makeSound() {
        print("Woof!")
    }
    
    func wagTail() {
        print("\(name) is wagging tail")
    }
}

// Usage
let dog = Dog(name: "Buddy", breed: "Golden Retriever")
dog.makeSound() // "Woof!"
dog.wagTail()   // "Buddy is wagging tail"
```

### Q8: What are access control levels in Swift?

**Answer:**
Swift has five access control levels:

```swift
// 1. open - Most permissive, allows subclassing outside module
open class OpenClass {
    open func openMethod() {}
}

// 2. public - Accessible from other modules, no subclassing
public class PublicClass {
    public func publicMethod() {}
}

// 3. internal - Default level, accessible within same module
internal class InternalClass { // 'internal' is optional
    func internalMethod() {}
}

// 4. fileprivate - Accessible within same file
class MyClass {
    fileprivate var filePrivateProperty = "Visible in this file"
}

// 5. private - Accessible only within declaring scope
class AnotherClass {
    private var privateProperty = "Only visible in this class"
    
    private func privateMethod() {
        print(privateProperty) // ✅ Accessible here
    }
}

extension AnotherClass {
    func extensionMethod() {
        print(privateProperty) // ✅ Accessible in extensions (same file)
    }
}
```

---

## Protocol-Oriented Programming

### Q9: What are protocols in Swift?

**Answer:**
Protocols define a blueprint of methods, properties, and requirements that classes, structs, or enums can adopt.

```swift
// Protocol definition
protocol Drawable {
    var area: Double { get }
    func draw()
    mutating func resize(by factor: Double)
}

// Protocol implementation
struct Circle: Drawable {
    var radius: Double
    
    var area: Double {
        return Double.pi * radius * radius
    }
    
    func draw() {
        print("Drawing a circle with radius \(radius)")
    }
    
    mutating func resize(by factor: Double) {
        radius *= factor
    }
}

// Multiple protocol conformance
protocol Named {
    var name: String { get }
}

struct Rectangle: Drawable, Named {
    var width: Double
    var height: Double
    var name: String
    
    var area: Double {
        return width * height
    }
    
    func draw() {
        print("Drawing rectangle \(name)")
    }
    
    mutating func resize(by factor: Double) {
        width *= factor
        height *= factor
    }
}
```

### Q10: What are protocol extensions?

**Answer:**
Protocol extensions allow you to provide default implementations for protocol methods.

```swift
protocol Printable {
    var description: String { get }
    func print()
}

// Protocol extension with default implementation
extension Printable {
    func print() {
        Swift.print(description) // Default implementation
    }
    
    func printWithTimestamp() {
        Swift.print("[\(Date())] \(description)")
    }
}

struct User: Printable {
    let name: String
    let age: Int
    
    var description: String {
        return "User: \(name), Age: \(age)"
    }
    
    // Can override default implementation
    func print() {
        Swift.print("🧑 \(description)")
    }
}

let user = User(name: "Alice", age: 30)
user.print() // "🧑 User: Alice, Age: 30"
user.printWithTimestamp() // "[timestamp] User: Alice, Age: 30"
```

### Q11: Explain delegation pattern in Swift

**Answer:**
Delegation is a design pattern where one object delegates responsibility to another object.

```swift
// 1. Define delegate protocol
protocol NetworkManagerDelegate: AnyObject {
    func networkManager(_ manager: NetworkManager, didReceiveData data: Data)
    func networkManager(_ manager: NetworkManager, didFailWithError error: Error)
}

// 2. Create delegating class
class NetworkManager {
    weak var delegate: NetworkManagerDelegate? // weak to avoid retain cycle
    
    func fetchData(from url: URL) {
        // Simulate network call
        DispatchQueue.global().async {
            // Simulate success
            let mockData = "Response data".data(using: .utf8)!
            
            DispatchQueue.main.async {
                self.delegate?.networkManager(self, didReceiveData: mockData)
            }
        }
    }
}

// 3. Implement delegate
class ViewController: NetworkManagerDelegate {
    let networkManager = NetworkManager()
    
    init() {
        networkManager.delegate = self
    }
    
    func loadData() {
        networkManager.fetchData(from: URL(string: "https://api.example.com")!)
    }
    
    // Delegate methods
    func networkManager(_ manager: NetworkManager, didReceiveData data: Data) {
        print("Received data: \(data)")
    }
    
    func networkManager(_ manager: NetworkManager, didFailWithError error: Error) {
        print("Error: \(error)")
    }
}
```

---

## Memory Management

### Q12: Explain ARC (Automatic Reference Counting)

**Answer:**
ARC automatically manages memory by keeping track of references to class instances.

```swift
class Person {
    let name: String
    
    init(name: String) {
        self.name = name
        print("\(name) is initialized")
    }
    
    deinit {
        print("\(name) is deallocated")
    }
}

// Reference counting example
var person1: Person? = Person(name: "John") // Reference count: 1
var person2 = person1                       // Reference count: 2
var person3 = person1                       // Reference count: 3

person1 = nil // Reference count: 2
person2 = nil // Reference count: 1
person3 = nil // Reference count: 0 -> object deallocated
```

### Q13: What are retain cycles and how do you break them?

**Answer:**
Retain cycles occur when two objects hold strong references to each other, preventing deallocation.

```swift
// ❌ Retain cycle example
class Parent {
    var child: Child?
    
    deinit {
        print("Parent deallocated")
    }
}

class Child {
    var parent: Parent? // Strong reference creates cycle
    
    deinit {
        print("Child deallocated")
    }
}

// Create cycle
var parent: Parent? = Parent()
var child: Child? = Child()
parent?.child = child
child?.parent = parent

parent = nil
child = nil
// Neither deinit is called - memory leak!

// ✅ Solution 1: weak reference
class Child2 {
    weak var parent: Parent? // weak breaks the cycle
    
    deinit {
        print("Child deallocated")
    }
}

// ✅ Solution 2: unowned reference
class Child3 {
    unowned var parent: Parent // unowned when child can't exist without parent
    
    init(parent: Parent) {
        self.parent = parent
    }
    
    deinit {
        print("Child deallocated")
    }
}
```

### Q14: What's the difference between weak and unowned?

**Answer:**

| Feature | weak | unowned |
|---------|------|---------|
| **Nullability** | Always optional | Non-optional |
| **Safety** | Safe - becomes nil when deallocated | Unsafe - crashes if accessed after deallocation |
| **Use Case** | When referenced object can be nil | When referenced object should never be nil |

```swift
class Customer {
    let name: String
    var card: CreditCard?
    
    init(name: String) {
        self.name = name
    }
    
    deinit {
        print("\(name) customer deallocated")
    }
}

class CreditCard {
    let number: UInt64
    unowned let customer: Customer // Credit card can't exist without customer
    
    init(number: UInt64, customer: Customer) {
        self.number = number
        self.customer = customer
    }
    
    deinit {
        print("Card #\(number) deallocated")
    }
}

// Usage
var john: Customer? = Customer(name: "John")
john?.card = CreditCard(number: 1234_5678_9012_3456, customer: john!)

john = nil // Both customer and card are deallocated
```

---

## Closures and Functions

### Q15: What are closures in Swift?

**Answer:**
Closures are self-contained blocks of functionality that can be passed around and used in your code.

```swift
// Basic closure syntax
let greetingClosure = { (name: String) -> String in
    return "Hello, \(name)!"
}

print(greetingClosure("Alice")) // "Hello, Alice!"

// Shorthand syntax
let numbers = [1, 2, 3, 4, 5]

// Full syntax
let doubled1 = numbers.map({ (number: Int) -> Int in
    return number * 2
})

// Shortened syntax
let doubled2 = numbers.map { $0 * 2 }

// Trailing closure syntax
let filtered = numbers.filter { $0 % 2 == 0 }
```

### Q16: Explain escaping and non-escaping closures

**Answer:**
- **Non-escaping** (default) - Closure is called before function returns
- **Escaping** - Closure can be called after function returns

```swift
class NetworkManager {
    var completionHandlers: [() -> Void] = []
    
    // Non-escaping closure (default)
    func performSync(completion: () -> Void) {
        // Closure must be called before function returns
        completion()
    }
    
    // Escaping closure
    func performAsync(completion: @escaping () -> Void) {
        // Closure stored and called later
        completionHandlers.append(completion)
        
        DispatchQueue.global().async {
            // Simulate async work
            DispatchQueue.main.async {
                completion() // Called after function returns
            }
        }
    }
}

// Usage
let manager = NetworkManager()

manager.performSync {
    print("Sync completion") // Called immediately
}

manager.performAsync {
    print("Async completion") // Called later
}
```

### Q17: What are capture lists in closures?

**Answer:**
Capture lists control how closures capture variables from their surrounding context.

```swift
class Counter {
    var count = 0
    
    func increment() {
        count += 1
    }
    
    deinit {
        print("Counter deallocated")
    }
}

// ❌ Strong reference cycle
func createStrongCycle() -> () -> Void {
    let counter = Counter()
    
    let closure = {
        counter.increment() // Strong reference to counter
        print("Count: \(counter.count)")
    }
    
    return closure
}

// ✅ Break cycle with weak capture
func createWeakCapture() -> () -> Void {
    let counter = Counter()
    
    let closure = { [weak counter] in
        counter?.increment() // Weak reference
        print("Count: \(counter?.count ?? 0)")
    }
    
    return closure
}

// ✅ Unowned capture
func createUnownedCapture() -> () -> Void {
    let counter = Counter()
    
    let closure = { [unowned counter] in
        counter.increment() // Unowned reference
        print("Count: \(counter.count)")
    }
    
    return closure
}

// ✅ Value capture
func createValueCapture() -> () -> Void {
    var localCount = 0
    
    let closure = { [localCount] in // Captures value, not reference
        print("Captured count: \(localCount)")
    }
    
    localCount = 10 // Doesn't affect captured value
    
    return closure
}
```

---

## Error Handling

### Q18: How does error handling work in Swift?

**Answer:**
Swift uses a structured approach to error handling with `throw`, `try`, `catch`, and `do-catch` blocks.

```swift
// 1. Define custom errors
enum NetworkError: Error {
    case noInternet
    case serverError(code: Int)
    case invalidURL
    case decodingFailed
}

// Make errors more descriptive
extension NetworkError: LocalizedError {
    var errorDescription: String? {
        switch self {
        case .noInternet:
            return "No internet connection"
        case .serverError(let code):
            return "Server error with code: \(code)"
        case .invalidURL:
            return "Invalid URL"
        case .decodingFailed:
            return "Failed to decode response"
        }
    }
}

// 2. Throwing function
func fetchData(from urlString: String) throws -> Data {
    guard let url = URL(string: urlString) else {
        throw NetworkError.invalidURL
    }
    
    // Simulate network check
    let hasInternet = Bool.random()
    guard hasInternet else {
        throw NetworkError.noInternet
    }
    
    // Simulate server response
    let responseCode = Int.random(in: 200...500)
    guard responseCode == 200 else {
        throw NetworkError.serverError(code: responseCode)
    }
    
    return Data("Success".utf8)
}

// 3. Error handling methods

// Method 1: do-catch
func handleWithDoCatch() {
    do {
        let data = try fetchData(from: "https://api.example.com")
        print("Success: \(String(data: data, encoding: .utf8) ?? "")")
    } catch NetworkError.noInternet {
        print("Please check your internet connection")
    } catch NetworkError.serverError(let code) {
        print("Server error: \(code)")
    } catch {
        print("Unexpected error: \(error)")
    }
}

// Method 2: try?
func handleWithTryOptional() {
    if let data = try? fetchData(from: "https://api.example.com") {
        print("Success: \(String(data: data, encoding: .utf8) ?? "")")
    } else {
        print("Failed to fetch data")
    }
}

// Method 3: try!
func handleWithTryForced() {
    // Use only when you're certain it won't throw
    let data = try! fetchData(from: "https://api.example.com")
    print("Data: \(data)")
}
```

### Q19: What's the difference between `try`, `try?`, and `try!`?

**Answer:**

| Operator | Behavior | Error Handling |
|----------|----------|----------------|
| **`try`** | Must handle errors | Requires do-catch |
| **`try?`** | Returns optional | Returns nil on error |
| **`try!`** | Force unwrap | Crashes on error |

```swift
enum ValidationError: Error {
    case tooShort
    case tooLong
}

func validatePassword(_ password: String) throws -> Bool {
    if password.count < 6 {
        throw ValidationError.tooShort
    }
    if password.count > 20 {
        throw ValidationError.tooLong
    }
    return true
}

// try - requires error handling
do {
    let isValid = try validatePassword("abc")
    print("Password is valid: \(isValid)")
} catch {
    print("Validation failed: \(error)")
}

// try? - returns optional
let isValid1 = try? validatePassword("abc") // nil if error
if let valid = isValid1 {
    print("Password is valid")
} else {
    print("Password validation failed")
}

// try! - force unwrap (crashes if error)
let isValid2 = try! validatePassword("validpassword") // Crashes if throws
```

---

## Generics

### Q20: What are generics in Swift?

**Answer:**
Generics allow you to write flexible, reusable code that works with any type while maintaining type safety.

```swift
// Generic function
func swap<T>(_ a: inout T, _ b: inout T) {
    let temp = a
    a = b
    b = temp
}

// Usage
var x = 10
var y = 20
swap(&x, &y) // x = 20, y = 10

var str1 = "Hello"
var str2 = "World"
swap(&str1, &str2) // str1 = "World", str2 = "Hello"

// Generic type
struct Stack<Element> {
    private var items: [Element] = []
    
    mutating func push(_ item: Element) {
        items.append(item)
    }
    
    mutating func pop() -> Element? {
        return items.popLast()
    }
    
    func peek() -> Element? {
        return items.last
    }
    
    var isEmpty: Bool {
        return items.isEmpty
    }
}

// Usage
var intStack = Stack<Int>()
intStack.push(1)
intStack.push(2)
print(intStack.pop()) // Optional(2)

var stringStack = Stack<String>()
stringStack.push("Hello")
stringStack.push("World")
print(stringStack.pop()) // Optional("World")
```

### Q21: What are type constraints in generics?

**Answer:**
Type constraints limit the types that can be used with generics to ensure they have required capabilities.

```swift
// Protocol for constraint
protocol Comparable {
    static func < (lhs: Self, rhs: Self) -> Bool
    static func == (lhs: Self, rhs: Self) -> Bool
}

// Generic function with type constraint
func findMax<T: Comparable>(_ array: [T]) -> T? {
    guard !array.isEmpty else { return nil }
    
    var max = array[0]
    for item in array {
        if item > max {
            max = item
        }
    }
    return max
}

// Usage
let numbers = [3, 1, 4, 1, 5, 9]
let maxNumber = findMax(numbers) // 9

let strings = ["apple", "banana", "cherry"]
let maxString = findMax(strings) // "cherry"

// Multiple constraints
protocol Drawable {
    func draw()
}

protocol Colorable {
    var color: String { get set }
}

func drawColoredShape<T: Drawable & Colorable>(_ shape: T) {
    print("Drawing \(shape.color) shape")
    shape.draw()
}

// Where clause for complex constraints
func processItems<T, U>(_ items: [T], with processor: U) -> [String]
    where T: CustomStringConvertible, U: (T) -> String {
    
    return items.map { processor($0) }
}
```

---

## Advanced Topics

### Q22: What are keypaths in Swift?

**Answer:**
Keypaths provide a way to refer to properties without calling them, enabling dynamic property access.

```swift
struct Person {
    let name: String
    let age: Int
    let email: String
}

let people = [
    Person(name: "Alice", age: 30, email: "alice@example.com"),
    Person(name: "Bob", age: 25, email: "bob@example.com"),
    Person(name: "Charlie", age: 35, email: "charlie@example.com")
]

// Keypath usage
let nameKeyPath = \Person.name
let ageKeyPath = \Person.age

// Accessing properties via keypath
let firstPersonName = people[0][keyPath: nameKeyPath] // "Alice"
let firstPersonAge = people[0][keyPath: ageKeyPath] // 30

// Functional programming with keypaths
let names = people.map(\.name) // ["Alice", "Bob", "Charlie"]
let ages = people.map(\.age) // [30, 25, 35]

// Sorting with keypaths
let sortedByAge = people.sorted(by: \.age, using: <)
let sortedByName = people.sorted(by: \.name, using: <)

// Generic function using keypaths
func getValue<T, V>(from object: T, using keyPath: KeyPath<T, V>) -> V {
    return object[keyPath: keyPath]
}

let email = getValue(from: people[0], using: \.email)
```

### Q23: Explain property wrappers

**Answer:**
Property wrappers provide a way to define custom behavior for property storage and access.

```swift
// Basic property wrapper
@propertyWrapper
struct Capitalized {
    private var value: String = ""
    
    var wrappedValue: String {
        get { value }
        set { value = newValue.capitalized }
    }
}

// Usage
struct User {
    @Capitalized var firstName: String
    @Capitalized var lastName: String
}

var user = User()
user.firstName = "john"
user.lastName = "doe"
print(user.firstName) // "John"
print(user.lastName) // "Doe"

// Property wrapper with parameters
@propertyWrapper
struct Clamped<T: Comparable> {
    private var value: T
    private let range: ClosedRange<T>
    
    init(wrappedValue: T, _ range: ClosedRange<T>) {
        self.range = range
        self.value = min(max(wrappedValue, range.lowerBound), range.upperBound)
    }
    
    var wrappedValue: T {
        get { value }
        set { value = min(max(newValue, range.lowerBound), range.upperBound) }
    }
}

struct GameCharacter {
    @Clamped(0...100) var health: Int = 100
    @Clamped(0...10) var level: Int = 1
}

var character = GameCharacter()
character.health = 150 // Clamped to 100
character.level = -5   // Clamped to 0
print(character.health) // 100
print(character.level)  // 0

// Property wrapper with projected value
@propertyWrapper
struct Logged<T> {
    private var value: T
    private(set) var projectedValue: [T] = []
    
    init(wrappedValue: T) {
        self.value = wrappedValue
        self.projectedValue.append(wrappedValue)
    }
    
    var wrappedValue: T {
        get { value }
        set {
            projectedValue.append(newValue)
            value = newValue
        }
    }
}

struct Counter {
    @Logged var count: Int = 0
}

var counter = Counter()
counter.count = 1
counter.count = 2
counter.count = 3
print(counter.count) // 3
print(counter.$count) // [0, 1, 2, 3] - history of values
```

### Q24: What are result builders?

**Answer:**
Result builders (formerly function builders) allow you to create DSLs (Domain Specific Languages) by transforming sequences of statements into a single value.

```swift
// Basic result builder
@resultBuilder
struct ArrayBuilder<T> {
    static func buildBlock(_ components: T...) -> [T] {
        Array(components)
    }
    
    static func buildOptional(_ component: [T]?) -> [T] {
        component ?? []
    }
    
    static func buildEither(first component: [T]) -> [T] {
        component
    }
    
    static func buildEither(second component: [T]) -> [T] {
        component
    }
    
    static func buildArray(_ components: [[T]]) -> [T] {
        components.flatMap { $0 }
    }
}

// Function using result builder
func buildArray<T>(@ArrayBuilder<T> _ builder: () -> [T]) -> [T] {
    builder()
}

// Usage
let numbers = buildArray {
    1
    2
    3
    if Bool.random() {
        4
    }
    for i in 5...7 {
        i
    }
}

print(numbers) // [1, 2, 3, (4), 5, 6, 7]

// HTML DSL example
@resultBuilder
struct HTMLBuilder {
    static func buildBlock(_ components: String...) -> String {
        components.joined()
    }
    
    static func buildOptional(_ component: String?) -> String {
        component ?? ""
    }
    
    static func buildEither(first component: String) -> String {
        component
    }
    
    static func buildEither(second component: String) -> String {
        component
    }
}

func html(@HTMLBuilder _ content: () -> String) -> String {
    "<html>\(content())</html>"
}

func body(@HTMLBuilder _ content: () -> String) -> String {
    "<body>\(content())</body>"
}

func p(_ text: String) -> String {
    "<p>\(text)</p>"
}

func h1(_ text: String) -> String {
    "<h1>\(text)</h1>"
}

// Usage
let webpage = html {
    body {
        h1("Welcome")
        p("This is a paragraph")
        if Bool.random() {
            p("Random paragraph")
        }
    }
}
```

### Q25: Explain async/await in Swift

**Answer:**
Async/await provides a structured way to write asynchronous code that's easy to read and maintain.

```swift
// Async function
func fetchUser(id: Int) async throws -> User {
    let url = URL(string: "https://api.example.com/user/\(id)")!
    let (data, _) = try await URLSession.shared.data(from: url)
    return try JSONDecoder().decode(User.self, from: data)
}

func fetchUserPosts(userId: Int) async throws -> [Post] {
    let url = URL(string: "https://api.example.com/user/\(userId)/posts")!
    let (data, _) = try await URLSession.shared.data(from: url)
    return try JSONDecoder().decode([Post].self, from: data)
}

// Sequential async calls
func loadUserData(id: Int) async throws -> (User, [Post]) {
    let user = try await fetchUser(id: id)
    let posts = try await fetchUserPosts(userId: id)
    return (user, posts)
}

// Parallel async calls
func loadUserDataParallel(id: Int) async throws -> (User, [Post]) {
    async let user = fetchUser(id: id)
    async let posts = fetchUserPosts(userId: id)
    
    return try await (user, posts)
}

// Task groups for dynamic parallelism
func loadMultipleUsers(ids: [Int]) async throws -> [User] {
    return try await withThrowingTaskGroup(of: User.self) { group in
        for id in ids {
            group.addTask {
                try await fetchUser(id: id)
            }
        }
        
        var users: [User] = []
        for try await user in group {
            users.append(user)
        }
        return users
    }
}

// Actor for thread-safe state
actor UserCache {
    private var cache: [Int: User] = [:]
    
    func getUser(id: Int) -> User? {
        return cache[id]
    }
    
    func setUser(_ user: User, for id: Int) {
        cache[id] = user
    }
}

// Usage in SwiftUI or other contexts
@MainActor
class UserViewModel: ObservableObject {
    @Published var user: User?
    @Published var posts: [Post] = []
    @Published var isLoading = false
    
    private let cache = UserCache()
    
    func loadUser(id: Int) async {
        isLoading = true
        
        do {
            // Check cache first
            if let cachedUser = await cache.getUser(id: id) {
                user = cachedUser
            } else {
                let fetchedUser = try await fetchUser(id: id)
                user = fetchedUser
                await cache.setUser(fetchedUser, for: id)
            }
            
            posts = try await fetchUserPosts(userId: id)
        } catch {
            print("Error loading user: \(error)")
        }
        
        isLoading = false
    }
}
```

This comprehensive Swift interview guide covers all the essential topics from basic concepts to advanced features. Each question includes practical examples and explanations that demonstrate real-world usage patterns.
