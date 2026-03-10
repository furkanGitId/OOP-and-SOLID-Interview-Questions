# C# OOP and SOLID Interview Questions

This repository contains a curated list of 10 common Object-Oriented Programming (OOP) and SOLID design principle questions for C# developers. Each question is answered with a simple Hinglish explanation and a practical C# code example to illustrate the concept.

## Table of Contents

1.  [What is method signature?](#1-what-is-method-signature)
2.  [What is the Order of constructor execution?](#2-what-is-the-order-of-constructor-execution)
3.  [What is destructor and how many can be there in a class (only 1)?](#3-what-is-destructor-and-how-many-can-be-there-in-a-class-only-1)
4.  [Why do you need a destructor when GC is there?](#4-why-do-you-need-a-destructor-when-gc-is-there)
5.  [What happens when constructor is private?](#5-what-happens-when-constructor-is-private)
6.  [If an Abstract class has concrete method so can its child class have same method name and can it modify it?](#6-if-an-abstract-class-has-concrete-method-so-can-its-child-class-have-same-method-name-and-can-it-modify-it)
7.  [Abstract vs Static class](#7-abstract-vs-static-class)
8.  [Abstract vs Interface](#8-abstract-vs-interface)
9.  [Why abstract class has constructors](#9-why-abstract-class-has-constructors)
10. [When to use abstract and when to use interface give real time example in c#](#10-when-to-use-abstract-and-when-to-use-interface-real-time-example-in-c)

---

# OOP / SOLID Interview Questions

## 1. What is method signature?

C# mein, ek **method signature** kisi class ya struct ke andar ek method ko uniquely identify karta hai. Isme method ka naam aur uske parameters ka type aur order shamil hota hai. Method ka return type aur method modifiers (jaise `public`, `private`, `static`, `virtual`, `abstract`, `override`) method signature ka part **nahi** hote. Yeh signature method overloading ke liye bahut important hai, jahan multiple methods ka naam same ho sakta hai lekin unke parameter lists alag hote hain.

### C# Example:

```csharp
using System;

public class Calculator
{
    // Method signature: Add(int, int)
    public int Add(int a, int b)
    {
        return a + b;
    }

    // Method signature: Add(double, double)
    public double Add(double a, double b)
    {
        return a + b;
    }

    // Method signature: Add(int, int, int)
    public int Add(int a, int b, int c)
    {
        return a + b + c;
    }

    // This would be a compile-time error because the return type is not part of the signature.
    // public double Add(int a, int b) { return (double)a + b; }

    public void DisplayResult(int result)
    {
        Console.WriteLine($"Integer Result: {result}");
    }

    public void DisplayResult(double result)
    {
        Console.WriteLine($"Double Result: {result}");
    }

    public static void Main(string[] args)
    {
        Calculator calc = new Calculator();

        Console.WriteLine($"Sum of 5 and 10: {calc.Add(5, 10)}");
        Console.WriteLine($"Sum of 5.5 and 10.5: {calc.Add(5.5, 10.5)}");
        Console.WriteLine($"Sum of 1, 2, and 3: {calc.Add(1, 2, 3)}");

        calc.DisplayResult(15);
        calc.DisplayResult(16.0);
    }
}
```

Upar diye gaye example mein, `Add` method ko alag-alag parameter types aur counts ke saath overload kiya gaya hai, jo dikhata hai ki method signature unhe kaise differentiate karta hai. Isi tarah, `DisplayResult` bhi apne parameter type ke basis par overloaded hai.

## 2. What is the Order of constructor execution?

Jab C# mein ek object banaya jata hai (instantiate hota hai), toh constructors ek specific order mein execute hote hain, khaaskar inheritance hierarchy mein. Order yeh hai:

1.  **Static constructors** pehle base class ke execute hote hain, uske baad derived classes ke static constructors. Static constructors ek application domain mein sirf ek baar call hote hain jab class ko pehli baar access kiya jata hai ya uska instance banaya jata hai.
2.  **Instance constructors** base class ke, derived class ke instance constructors se pehle execute hote hain. Yeh ensure karta hai ki base class theek se initialize ho jaye usse pehle ki derived class apni functionalities add kare.

Yeh order Common Language Runtime (CLR) dwara enforce kiya jata hai, taaki object ka proper initialization ho aur derived classes uninitialized base class members par operate na karein.

### C# Example:

```csharp
using System;

public class BaseClass
{
    static BaseClass()
    {
        Console.WriteLine("BaseClass Static Constructor");
    }

    public BaseClass()
    {
        Console.WriteLine("BaseClass Instance Constructor");
    }
}

public class DerivedClass : BaseClass
{
    static DerivedClass()
    {
        Console.WriteLine("DerivedClass Static Constructor");
    }

    public DerivedClass()
    {
        Console.WriteLine("DerivedClass Instance Constructor");
    }
}

public class Program
{
    public static void Main(string[] args)
    {
        Console.WriteLine("Creating DerivedClass instance...");
        DerivedClass obj = new DerivedClass();
        Console.WriteLine("DerivedClass instance created.");

        Console.WriteLine("\nCreating another DerivedClass instance...");
        DerivedClass obj2 = new DerivedClass(); // Static constructors won't run again
        Console.WriteLine("Another DerivedClass instance created.");
    }
}
```

**Expected Output:**

```
Creating DerivedClass instance...
BaseClass Static Constructor
DerivedClass Static Constructor
BaseClass Instance Constructor
DerivedClass Instance Constructor
DerivedClass instance created.

Creating another DerivedClass instance...
BaseClass Instance Constructor
DerivedClass Instance Constructor
Another DerivedClass instance created.
```

Output mein dekha ja sakta hai, static constructors pehle aur sirf ek baar run hote hain, uske baad base class instance constructor, aur phir har object creation ke liye derived class instance constructor run hota hai.

## 3. What is destructor and how many can be there in a class (only 1)?

C# mein, ek **destructor** class ke andar ek special method hota hai jo automatically execute hota hai jab class ka instance ab zaroorat mein nahi hota aur garbage collected hone wala hota hai. Iska main purpose unmanaged resources (jaise file handles, network connections, database connections) ko release karna hota hai, jo object ne hold kiye ho sakte hain.

C# mein, ek class mein **sirf ek destructor** ho sakta hai. Destructors koi parameters nahi lete, access modifiers (implicitly `private`) nahi ho sakte, aur unhe explicitly call nahi kiya ja sakta. Unhe class name ke aage tilde (`~`) lagakar identify kiya jata hai.

### C# Example:

```csharp
using System;
using System.IO;

public class MyResourceHolder
{
    private StreamWriter _logFile;

    public MyResourceHolder(string filePath)
    {
        Console.WriteLine($"MyResourceHolder constructor called for {filePath}");
        _logFile = new StreamWriter(filePath, true);
        _logFile.WriteLine($"Log started at {DateTime.Now}");
    }

    public void WriteMessage(string message)
    {
        _logFile.WriteLine($"Message: {message}");
        _logFile.Flush(); // Ensure data is written to file
    }

    // Destructor
    ~MyResourceHolder()
    {
        Console.WriteLine("MyResourceHolder destructor called. Releasing resources.");
        if (_logFile != null)
        {
            _logFile.WriteLine($"Log ended at {DateTime.Now}");
            _logFile.Dispose(); // Close and release the file handle
            Console.WriteLine("Log file closed.");
        }
    }
}

public class Program
{
    public static void CreateAndDisposeObject()
    {
        MyResourceHolder resource = new MyResourceHolder("app_log.txt");
        resource.WriteMessage("Performing some operation.");
        // The object 'resource' goes out of scope here.
        // The destructor will eventually be called by the Garbage Collector.
    }

    public static void Main(string[] args)
    {
        Console.WriteLine("Starting program...");
        CreateAndDisposeObject();

        // Force garbage collection for demonstration purposes. 
        // In real applications, avoid calling GC.Collect() explicitly.
        GC.Collect();
        GC.WaitForPendingFinalizers(); // Wait for destructors to complete

        Console.WriteLine("Program finished.");
    }
}
```

Is example mein, `~MyResourceHolder()` destructor ensure karta hai ki `StreamWriter` (ek unmanaged resource) theek se close ho jaye jab `MyResourceHolder` object garbage collected hota hai. Yeh resource leaks ko rokta hai.

## 4. Why do you need a destructor when GC is there?

Jabki .NET Garbage Collector (GC) managed memory ko effectively manage karta hai, destructors (ya .NET mein finalizers) ab bhi **unmanaged resources** ko handle karne ke liye zaroori hain. GC managed objects dwara occupy ki gayi memory ko reclaim karne ke liye design kiya gaya hai, lekin usse managed heap ke bahar ke resources ke baare mein koi knowledge ya control nahi hota, jaise:

*   File handles
*   Network connections
*   Database connections
*   Graphics device interfaces (GDI) objects
*   Pointers to unmanaged memory

Agar yeh unmanaged resources explicitly release nahi kiye jaate, toh unse **resource leaks** ho sakte hain, bhale hi object se associated managed memory GC dwara reclaim ho jaye. Destructors is cleanup ko perform karne ka ek mechanism provide karte hain. Lekin, yeh note karna important hai ki:

*   **Destructors non-deterministic hote hain:** GC decide karta hai ki finalizers kab run honge, isliye koi guarantee nahi hoti ki cleanup kab hoga. Isse resources zaroorat se zyada der tak hold ho sakte hain.
*   **Performance overhead:** Finalization se garbage collection process mein overhead badh jaata hai.

In reasons ki wajah se, C# mein unmanaged resources ko deterministically release karne ka preferred pattern **`IDisposable` interface** aur `using` statement hai. Yeh developers ko control deta hai ki resources kab release honge, bajaye GC par depend karne ke.

### C# Example (Illustrating `IDisposable` as the preferred approach):

```csharp
using System;
using System.IO;

// Implementing IDisposable for deterministic resource release
public class ManagedResourceHolder : IDisposable
{
    private StreamWriter _logFile;
    private bool disposed = false;

    public ManagedResourceHolder(string filePath)
    {
        Console.WriteLine($"ManagedResourceHolder constructor called for {filePath}");
        _logFile = new StreamWriter(filePath, true);
        _logFile.WriteLine($"Log started at {DateTime.Now}");
    }

    public void WriteMessage(string message)
    {
        if (disposed)
        {
            throw new ObjectDisposedException(nameof(ManagedResourceHolder));
        }
        _logFile.WriteLine($"Message: {message}");
        _logFile.Flush();
    }

    // Public implementation of Dispose pattern callable by consumers.
    public void Dispose()
    {
        Dispose(true);
        GC.SuppressFinalize(this); // Prevent the destructor from running
    }

    // Protected virtual method to support derived classes
    protected virtual void Dispose(bool disposing)
    {
        if (!disposed)
        {
            if (disposing)
            {
                // Dispose managed resources here
                if (_logFile != null)
                {
                    _logFile.WriteLine($"Log ended at {DateTime.Now}");
                    _logFile.Dispose(); // Release managed resource
                    _logFile = null;
                    Console.WriteLine("Managed log file closed via Dispose.");
                }
            }
            // Dispose unmanaged resources here (if any)
            disposed = true;
        }
    }

    // Destructor (Finalizer) - only called if Dispose was not called explicitly
    ~ManagedResourceHolder()
    {
        Console.WriteLine("ManagedResourceHolder destructor called. (Should only happen if Dispose() was not called)");
        Dispose(false); // Clean up unmanaged resources
    }
}

public class Program
{
    public static void Main(string[] args)
    {
        Console.WriteLine("Starting program with IDisposable...");

        // Using statement ensures Dispose() is called automatically
        using (ManagedResourceHolder resource = new ManagedResourceHolder("app_log_managed.txt"))
        {
            resource.WriteMessage("Performing some operation with managed resource.");
        }
        // Dispose() is called here automatically when exiting the using block.

        Console.WriteLine("Program finished. Destructor should NOT be called for the above object.");

        Console.WriteLine("\nDemonstrating potential resource leak without Dispose() or using statement...");
        ManagedResourceHolder leakyResource = new ManagedResourceHolder("app_log_leaky.txt");
        leakyResource.WriteMessage("This resource might leak if not disposed.");
        // No Dispose() called. Destructor will eventually run, but non-deterministically.

        GC.Collect();
        GC.WaitForPendingFinalizers();

        Console.WriteLine("End of program.");
    }
}
```

Is example mein, `IDisposable` `StreamWriter` ko release karne ka ek deterministic tareeka provide karta hai. Destructor (`~ManagedResourceHolder()`) ek fallback ke roop mein kaam karta hai un cases mein jahan `Dispose()` call nahi kiya jata, lekin is par primary resource cleanup ke liye depend karna generally discourage kiya jata hai, iski non-deterministic nature aur performance implications ki wajah se.

## 5. What happens when constructor is private?

Jab C# mein ek constructor ko `private` declare kiya jata hai, toh iska matlab hai ki class ko uske bahar se directly instantiate nahi kiya ja sakta. Yeh design pattern mainly do scenarios ke liye use hota hai:

1.  **Singleton Pattern:** Yeh ensure karta hai ki ek class ka sirf ek hi instance exist kare. Private constructor external code ko naye instances banane se rokta hai, aur ek public static method (aksar `Instance` ya `GetInstance` naam se) single instance tak controlled access provide karta hai.
2.  **Static Classes with Utility Methods:** Bhale hi C# mein explicit `static` classes hain, kabhi-kabhi ek class jismein sirf static members hote hain, usmein bhi private constructor ho sakta hai taaki instantiation ko explicitly roka ja sake, bhale hi usmein koi instance state na ho. Compiler implicitly static classes mein ek private constructor add karta hai, lekin aap use explicitly define kar sakte hain.
3.  **Factory Methods:** Private constructor ko public static factory methods ke saath use kiya ja sakta hai jo objects ke creation ko control karte hain, aur input parameters ke basis par alag-alag subclasses ya pre-configured instances return kar sakte hain.

`new` keyword ka use karke private constructor wali class ka instance directly bahar se banane ki koshish karne par compile-time error aayega.

### C# Example (Singleton Pattern):

```csharp
using System;

public class Singleton
{
    // Static instance of the class
    private static Singleton _instance;
    private static readonly object _lock = new object();

    // Private constructor prevents direct instantiation
    private Singleton()
    {
        Console.WriteLine("Singleton instance created.");
    }

    // Public static method to get the single instance
    public static Singleton GetInstance()
    {
        // Double-checked locking for thread safety
        if (_instance == null)
        {
            lock (_lock)
            {
                if (_instance == null)
                {
                    _instance = new Singleton();
                }
            }
        }
        return _instance;
    }

    public void ShowMessage()
    {
        Console.WriteLine("Hello from the Singleton instance!");
    }
}

public class Program
{
    public static void Main(string[] args)
    {
        // Attempting to create an instance directly will result in a compile-time error:
        // Singleton s1 = new Singleton(); // Error: 'Singleton.Singleton()' is inaccessible due to its protection level

        // Get the single instance via the static method
        Singleton s1 = Singleton.GetInstance();
        s1.ShowMessage();

        Singleton s2 = Singleton.GetInstance();
        s2.ShowMessage();

        // Verify that both references point to the same instance
        if (s1 == s2)
        {
            Console.WriteLine("s1 and s2 refer to the same instance.");
        }
    }
}
```

Is example mein, `Singleton` class ka private constructor hai, jo ensure karta hai ki `GetInstance()` hi class ka instance obtain karne ka ekmatra tareeka hai. Yeh guarantee karta hai ki application ke lifetime mein `Singleton` ka sirf ek hi instance exist karega.

## 6. If an Abstract class has concrete method so can its child class have same method name and can it modify it?

Haan, agar C# mein ek **abstract class** mein ek **concrete method** (ek method jismein implementation ho) hai, toh uski child class (ek concrete class ya koi aur abstract class) mein bhi same naam ka method ho sakta hai. Child class abstract base class ke concrete method ke behavior ko do main mechanisms ke through modify ya extend kar sakti hai:

1.  **Method Hiding (`new` keyword ka use karke):** Child class base class ke concrete method ke same naam ka ek naya method declare kar sakti hai. Yeh explicitly indicate karne ke liye ki yeh intentional hai aur accidental redefinition nahi, `new` keyword ka use karna chahiye. Yeh base class method ko child class ke scope se hide kar deta hai jab child class type ke through access kiya jata hai, lekin base class method ko ab bhi base class type mein child class instance ko cast karke access kiya ja sakta hai.
2.  **Method Overriding (`virtual` aur `override` keywords ka use karke):** Agar abstract base class mein concrete method ko `virtual` declare kiya gaya hai, toh child class use `override` kar sakti hai. Iska matlab hai ki child class method ke liye ek naya implementation provide karti hai, aur yeh naya implementation call hota hai chahe object ko base class type ya derived class type (polymorphism) ke through access kiya jaye.

### C# Example:

```csharp
using System;

public abstract class AbstractBase
{
    // Concrete method in abstract class
    public void DisplayMessage()
    {
        Console.WriteLine("AbstractBase: This is a concrete method.");
    }

    // Virtual concrete method in abstract class
    public virtual void ProcessData()
    {
        Console.WriteLine("AbstractBase: Processing data (default implementation).");
    }

    // Abstract method (must be implemented by derived classes)
    public abstract void AbstractMethod();
}

public class ConcreteDerived : AbstractBase
{
    // 1. Method Hiding: Hides the DisplayMessage from AbstractBase
    public new void DisplayMessage()
    {
        Console.WriteLine("ConcreteDerived: This is a NEW DisplayMessage method (hiding base).");
    }

    // 2. Method Overriding: Provides a new implementation for ProcessData
    public override void ProcessData()
    {
        Console.WriteLine("ConcreteDerived: Processing data (overridden implementation).");
    }

    // Implementation of the abstract method
    public override void AbstractMethod()
    {
        Console.WriteLine("ConcreteDerived: Implementation of AbstractMethod.");
    }
}

public class Program
{
    public static void Main(string[] args)
    {
        ConcreteDerived derived = new ConcreteDerived();
        derived.DisplayMessage(); // Calls the new method in ConcreteDerived
        derived.ProcessData();    // Calls the overridden method in ConcreteDerived
        derived.AbstractMethod(); // Calls the implemented abstract method

        Console.WriteLine("\nAccessing via Base Class Reference:");
        AbstractBase baseRef = derived;
        baseRef.DisplayMessage(); // Calls the original method in AbstractBase (because it's hidden, not overridden)
        baseRef.ProcessData();    // Still calls the overridden method in ConcreteDerived (polymorphism)
        baseRef.AbstractMethod(); // Still calls the implemented abstract method
    }
}
```

**Output:**

```
ConcreteDerived: This is a NEW DisplayMessage method (hiding base).
ConcreteDerived: Processing data (overridden implementation).
ConcreteDerived: Implementation of AbstractMethod.

Accessing via Base Class Reference:
AbstractBase: This is a concrete method.
ConcreteDerived: Processing data (overridden implementation).
ConcreteDerived: Implementation of AbstractMethod.
```

Yeh example method hiding aur method overriding ke beech ka difference clearly dikhata hai. Jab `ProcessData` ko `baseRef` ke through call kiya jata hai, toh `ConcreteDerived` mein overridden version polymorphism ki wajah se execute hota hai. Lekin, `DisplayMessage` ke liye, base class version call hota hai jab `AbstractBase` reference ke through access kiya jata hai kyunki derived class method sirf use hide karta hai, override nahi.

## 7. Abstract vs Static class

C# mein **Abstract classes** aur **static classes** alag-alag purposes ke liye use hoti hain, bhale hi dono ko directly instantiate nahi kiya ja sakta. Main differences unke design intent, inheritance capabilities, aur member types mein hain.

| Feature             | Abstract Class                                        | Static Class                                          |
| :------------------ | :---------------------------------------------------- | :---------------------------------------------------- |
| **Instantiation**   | Cannot be instantiated directly. Requires a derived class to be instantiated. | Cannot be instantiated directly.                      |
| **Inheritance**     | Can be inherited by other classes. Can implement interfaces. | Cannot be inherited by other classes. Cannot implement interfaces. |
| **Members**       Abstract (bina implementation ke) aur concrete (implementation ke saath) methods, properties, events, aur indexers dono ho sakte hain. Instance aur static members bhi ho sakte hain.| Can only have static members (methods, properties, events, fields). |
| **Constructors**    | Can have constructors (used by derived classes).      | Can have a static constructor (implicitly or explicitly defined), but no instance constructors. |
| **Polymorphism**    | Supports polymorphism through inheritance and method overriding. | Does not support polymorphism in the same way, as it cannot be inherited. |
| **Purpose**         | Derived classes ke liye ek common base provide karta hai, ek contract define karta hai aur potentially kuch default behavior bhi deta hai. Frameworks banane ke liye useful hai jahan kuch implementation details subclasses ke liye chhod diye jaate hain. | Utility functions ya data provide karta hai jo class se belong karte hain, kisi specific instance se nahi. Helper classes ya global functionality ke liye useful hai. |

### C# Example:

```csharp
using System;

// Abstract Class Example
public abstract class Shape
{
    public string Name { get; set; }

    public Shape(string name)
    {
        Name = name;
    }

    // Abstract method - must be implemented by derived classes
    public abstract double GetArea();

    // Concrete method - can be used as-is or overridden by derived classes
    public virtual void DisplayInfo()
    {
        Console.WriteLine($"Shape: {Name}");
    }
}

public class Circle : Shape
{
    public double Radius { get; set; }

    public Circle(string name, double radius) : base(name)
    {
        Radius = radius;
    }

    public override double GetArea()
    {
        return Math.PI * Radius * Radius;
    }

    public override void DisplayInfo()
    {
        base.DisplayInfo();
        Console.WriteLine($"Type: Circle, Radius: {Radius}, Area: {GetArea():F2}");
    }
}

// Static Class Example
public static class MathHelper
{
    public static double PiValue = Math.PI;

    public static double CalculateCircumference(double radius)
    {
        return 2 * PiValue * radius;
    }

    public static double ConvertCelsiusToFahrenheit(double celsius)
    {
        return (celsius * 9 / 5) + 32;
    }
}

public class Program
{
    public static void Main(string[] args)
    {
        Console.WriteLine("--- Abstract Class Usage ---");
        // Shape s = new Shape("Generic"); // Compile-time error: Cannot create an instance of the abstract type or interface 'Shape'

        Circle circle = new Circle("My Circle", 5);
        circle.DisplayInfo();
        Console.WriteLine($"Area of {circle.Name}: {circle.GetArea():F2}");

        Console.WriteLine("\n--- Static Class Usage ---");
        // MathHelper mh = new MathHelper(); // Compile-time error: 'MathHelper': cannot create an instance of static class

        Console.WriteLine($"Pi Value: {MathHelper.PiValue}");
        Console.WriteLine($"Circumference of circle with radius 5: {MathHelper.CalculateCircumference(5):F2}");
        Console.WriteLine($"25 Celsius is {MathHelper.ConvertCelsiusToFahrenheit(25):F2} Fahrenheit.");
    }
}
```

Is example mein, `Shape` ek abstract class hai jo alag-alag geometric shapes ke liye ek common structure provide karti hai, aur `Circle` jaisi derived classes ko `GetArea()` implement karna zaroori banati hai. `MathHelper` ek static class hai jo bina kisi instance creation ke utility functions provide karti hai.

## 8. Abstract vs Interface

C# mein **abstract classes** aur **interfaces** dono hi fundamental concepts hain jo classes ke liye contracts define karte hain, lekin unki capabilities, usage, aur design intent mein kaafi differences hain.

| Feature             | Abstract Class                                        | Interface                                             |
| :------------------ | :---------------------------------------------------- | :---------------------------------------------------- |
| **Base Type**       | Class                                                 | Reference type (similar to a class, but cannot be instantiated) |
| **Implementation**  | Can have both abstract (no implementation) and concrete (with implementation) methods, properties, events, and indexers. | C# 8.0 se pehle, sirf members declare kar sakte the (koi implementation nahi). C# 8.0 ke baad se, methods ke liye default implementations ho sakte hain. |
| **Access Modifiers**| Apne members ke liye various access modifiers (public, protected, internal, private) ho sakte hain. | Sabhi members implicitly `public` aur `abstract` hote hain (C# 8.0 se pehle). C# 8.0 ke baad se, default implementations mein access modifiers ho sakte hain. |
| **Inheritance**     | Ek class sirf ek abstract class se inherit kar sakti hai.     | Ek class multiple interfaces implement kar sakti hai.            |
| **Constructors**    | Constructors ho sakte hain.                                | Constructors nahi ho sakte.                             |
| **Fields**          | Fields (instance aur static) ho sakte hain.                | Fields nahi ho sakte (sirf properties).                 |
| **Purpose**         | Related objects ki hierarchy ke liye ek base define karta hai, common functionality provide karta hai aur ek common structure enforce karta hai. "Is-a" relationship. | Unrelated classes ke liye ek contract define karta hai, yeh specify karta hai ki ek class *kya kar sakti hai*. "Can-do" relationship. |

### C# Example:

```csharp
using System;

// Interface Example
public interface ILogger
{
    void LogMessage(string message);
    void LogError(string error);
}

// Abstract Class Example
public abstract class DataProcessor
{
    public string DataSource { get; protected set; }

    public DataProcessor(string dataSource)
    {
        DataSource = dataSource;
    }

    // Abstract method - must be implemented by derived classes
    public abstract void LoadData();

    // Concrete method - provides default implementation
    public virtual void ProcessData()
    {
        Console.WriteLine($"Processing data from {DataSource} using default logic.");
    }

    public void SaveProcessedData()
    {
        Console.WriteLine("Saving processed data.");
    }
}

// Class implementing an interface and inheriting from an abstract class
public class FileProcessor : DataProcessor, ILogger
{
    private string _logFilePath;

    public FileProcessor(string filePath, string logFilePath) : base(filePath)
    {
        _logFilePath = logFilePath;
    }

    // Implementation of abstract method from DataProcessor
    public override void LoadData()
    {
        Console.WriteLine($"Loading data from file: {DataSource}");
        // Simulate loading data
    }

    // Implementation of interface methods from ILogger
    public void LogMessage(string message)
    {
        Console.WriteLine($"[LOG] {DateTime.Now}: {message}");
        // In a real app, write to _logFilePath
    }

    public void LogError(string error)
    {
        Console.Error.WriteLine($"[ERROR] {DateTime.Now}: {error}");
        // In a real app, write to _logFilePath
    }

    // Can also override virtual methods from the abstract class
    public override void ProcessData()
    {
        Console.WriteLine($"FileProcessor: Custom processing data from {DataSource}.");
        LogMessage("Custom file processing started.");
    }
}

public class Program
{
    public static void Main(string[] args)
    {
        FileProcessor processor = new FileProcessor("data.txt", "app.log");
        processor.LoadData();
        processor.ProcessData();
        processor.SaveProcessedData();
        processor.LogMessage("Data processing completed.");
        processor.LogError("An error occurred during file access.");

        Console.WriteLine("\n--- Demonstrating Interface Polymorphism ---");
        ILogger logger = processor;
        logger.LogMessage("Message via ILogger reference.");

        Console.WriteLine("\n--- Demonstrating Abstract Class Polymorphism ---");
        DataProcessor dataProc = processor;
        dataProc.LoadData();
        dataProc.ProcessData();
    }
}
```

Is example mein, `ILogger` logging ke liye ek contract define karta hai, jise `FileProcessor` implement karta hai. `DataProcessor` ek abstract class hai jo data handling ke liye ek base provide karti hai, aur `FileProcessor` usse inherit karta hai. Yeh dikhata hai ki kaise ek class ek abstract class se inherit kar sakti hai aur multiple interfaces implement kar sakti hai, shared base functionality ko specific contractual behaviors ke saath combine karte hue.

## 9. Why abstract class has constructors

Bhale hi ek **abstract class** ko directly instantiate nahi kiya ja sakta, ismein constructors ho sakte hain (aur aksar hote hain). Abstract class mein constructors hone ka primary reason yeh hai ki jab ek concrete derived class instantiate hoti hai, toh yeh **apne base class members ki state ko initialize** kar sake. Jab ek derived class banayi jaati hai, toh uska constructor apne immediate base class ke constructor ko call karne ke liye responsible hota hai, jo ek abstract class ho sakti hai.

Abstract class constructors useful kyun hain, iske reasons yeh hain:

*   **Shared State ka Initialization:** Abstract classes common fields ya properties define kar sakti hain jo sabhi derived classes share karengi. Abstract class ka constructor in shared members ko initialize kar sakta hai, yeh ensure karte hue ki object ka base part derived class ke constructor execute hone se pehle valid state mein ho.
*   **Initialization Rules ko Enforce karna:** Constructor provide karke, ek abstract class enforce kar sakti hai ki kisi bhi derived class instance ke creation ke dauran certain parameters pass kiye jayein, yeh ensure karte hue ki object ke base part ke liye essential data provide kiya gaya hai.
*   **Dependency Injection:** Abstract class constructors ka use dependencies ko inject karne ke liye kiya ja sakta hai jo sabhi derived classes ke liye common hain.

Yeh yaad rakhna important hai ki ek abstract class ka constructor external code dwara **kabhi bhi directly call nahi kiya jata**. Ise hamesha indirectly ek concrete derived class ke constructor dwara `base` keyword ka use karke call kiya jata hai.

### C# Example:

```csharp
using System;

public abstract class Vehicle
{
    public string Make { get; protected set; }
    public string Model { get; protected set; }
    public int Year { get; protected set; }

    // Abstract class constructor
    public Vehicle(string make, string model, int year)
    {
        Make = make;
        Model = model;
        Year = year;
        Console.WriteLine($"Vehicle constructor called: {Year} {Make} {Model}");
    }

    public abstract void Start();
    public abstract void Stop();

    public void DisplayDetails()
    {
        Console.WriteLine($"Details: {Year} {Make} {Model}");
    }
}

public class Car : Vehicle
{
    public int NumberOfDoors { get; private set; }

    // Car constructor calls the base (Vehicle) constructor
    public Car(string make, string model, int year, int numberOfDoors) 
        : base(make, model, year)
    {
        NumberOfDoors = numberOfDoors;
        Console.WriteLine($"Car constructor called with {NumberOfDoors} doors.");
    }

    public override void Start()
    {
        Console.WriteLine($"The {Make} {Model} car starts with a key.");
    }

    public override void Stop()
    {
        Console.WriteLine($"The {Make} {Model} car stops.");
    }
}

public class Motorcycle : Vehicle
{
    public bool HasSideCar { get; private set; }

    // Motorcycle constructor calls the base (Vehicle) constructor
    public Motorcycle(string make, string model, int year, bool hasSideCar)
        : base(make, model, year)
    {
        HasSideCar = hasSideCar;
        Console.WriteLine($"Motorcycle constructor called. Has sidecar: {HasSideCar}");
    }

    public override void Start()
    {
        Console.WriteLine($"The {Make} {Model} motorcycle kicks to start.");
    }

    public override void Stop()
    {
        Console.WriteLine($"The {Make} {Model} motorcycle stops.");
    }
}

public class Program
{
    public static void Main(string[] args)
    {
        Console.WriteLine("Creating a Car object:");
        Car myCar = new Car("Toyota", "Camry", 2023, 4);
        myCar.DisplayDetails();
        myCar.Start();
        myCar.Stop();

        Console.WriteLine("\nCreating a Motorcycle object:");
        Motorcycle myMotorcycle = new Motorcycle("Harley-Davidson", "Sportster", 2022, false);
        myMotorcycle.DisplayDetails();
        myMotorcycle.Start();
        myMotorcycle.Stop();
    }
}
```

**Output:**

```
Creating a Car object:
Vehicle constructor called: 2023 Toyota Camry
Car constructor called with 4 doors.
Details: 2023 Toyota Camry
The Toyota Camry car starts with a key.
The Toyota Camry car stops.

Creating a Motorcycle object:
Vehicle constructor called: 2022 Harley-Davidson Sportster
Motorcycle constructor called. Has sidecar: False
Details: 2022 Harley-Davidson Sportster
The Harley-Davidson Sportster motorcycle kicks to start.
The Harley-Davidson Sportster motorcycle stops.
```

Yeh example dikhata hai ki jab `Car` aur `Motorcycle` objects banaye jaate hain, toh unke constructors implicitly ya explicitly pehle `Vehicle` abstract class constructor ko call karte hain, yeh ensure karte hue ki `Make`, `Model`, aur `Year` properties sabhi derived vehicle types ke liye correctly initialize ho.

## 10. When to use abstract and when to use interface (real-time example in C#)

Ek **abstract class** aur ek **interface** ke beech choose karna specific design requirements aur classes ke beech ke relationship par depend karta hai. Abstraction aur polymorphism achieve karne ke liye dono hi powerful tools hain, lekin ve alag-alag scenarios ke liye best suited hain.

### When to use an Abstract Class:

Abstract class ka use tab karein jab:

*   **Aap related classes ke liye ek common base implementation provide karna chahte hain:** Agar multiple classes mein kaafi common code ya state share ho rahi hai, toh ek abstract class yeh default implementation provide kar sakti hai, jisse code duplication kam hoti hai.
*   **Aap ek common interface (abstract methods) define karna chahte hain aur saath hi kuch concrete methods bhi provide karna chahte hain:** Abstract classes aapko ek contract (abstract methods) enforce karne ki permission deti hain jise derived classes ko implement karna hota hai, aur saath hi ready-to-use functionality (concrete methods) bhi offer karti hain.
*   **Aapko derived classes ke beech fields ya constructors share karne ki zaroorat hai:** Abstract classes mein fields aur constructors ho sakte hain, jo sabhi derived classes ke liye common state ko initialize karne ke liye useful hain.
*   **Aap ek aisa component design kar rahe hain jise versioning ki zaroorat hai:** Abstract classes interfaces se zyada easily evolve ho sakti hain. Aap ek abstract class mein naye concrete methods add kar sakte hain bina existing derived classes ko break kiye.
*   **Aapke paas "is-a" relationship hai:** Derived class abstract class ka ek type *hai* (jaise, `Car` ek `Vehicle` *hai*).

**Real-time C# Example (Abstract Class - Payment Gateway Integration):**

Various payment gateways (jaise PayPal, Stripe, Square) ke saath integrate karne par vichar karein. Jabki har gateway ka apna unique API hota hai, ve sabhi common operations share karte hain jaise payments process karna, refund karna, aur transaction status check karna. Ek abstract class ek common structure provide kar sakti hai.

```csharp
using System;

// Abstract class for a generic payment gateway
public abstract class PaymentGateway
{
    public string GatewayName { get; protected set; }
    public string ApiKey { get; protected set; }

    public PaymentGateway(string gatewayName, string apiKey)
    {
        GatewayName = gatewayName;
        ApiKey = apiKey;
        Console.WriteLine($"Initialized {GatewayName} Gateway with API Key: {apiKey.Substring(0, 4)}...");
    }

    // Abstract methods that all gateways must implement
    public abstract bool ProcessPayment(decimal amount, string currency, string cardNumber, string expiry, string cvv);
    public abstract bool RefundPayment(string transactionId, decimal amount);
    public abstract string GetTransactionStatus(string transactionId);

    // Concrete method with common logic (e.g., logging or basic validation)
    public virtual void LogTransaction(string transactionId, string status, decimal amount)
    {
        Console.WriteLine($"[{GatewayName}] Transaction {transactionId} - Status: {status}, Amount: {amount:C}");
    }
}

// Concrete implementation for Stripe
public class StripeGateway : PaymentGateway
{
    public StripeGateway(string apiKey) : base("Stripe", apiKey) { }

    public override bool ProcessPayment(decimal amount, string currency, string cardNumber, string expiry, string cvv)
    {
        Console.WriteLine($"Stripe: Processing {amount:C} {currency} for card {cardNumber.Substring(cardNumber.Length - 4)}...");
        // Simulate API call to Stripe
        return true; // Assume success
    }

    public override bool RefundPayment(string transactionId, decimal amount)
    {
        Console.WriteLine($"Stripe: Refunding {amount:C} for transaction {transactionId}...");
        // Simulate API call to Stripe
        return true; // Assume success
    }

    public override string GetTransactionStatus(string transactionId)
    {
        Console.WriteLine($"Stripe: Getting status for transaction {transactionId}...");
        // Simulate API call to Stripe
        return "Completed";
    }
}

// Concrete implementation for PayPal
public class PayPalGateway : PaymentGateway
{
    public PayPalGateway(string apiKey) : base("PayPal", apiKey) { }

    public override bool ProcessPayment(decimal amount, string currency, string cardNumber, string expiry, string cvv)
    {
        Console.WriteLine($"PayPal: Processing {amount:C} {currency} for card {cardNumber.Substring(cardNumber.Length - 4)}...");
        // Simulate API call to PayPal
        return true; // Assume success
    }

    public override bool RefundPayment(string transactionId, decimal amount)
    {
        Console.WriteLine($"PayPal: Refunding {amount:C} for transaction {transactionId}...");
        // Simulate API call to PayPal
        return true; // Assume success
    }

    public override string GetTransactionStatus(string transactionId)
    {
        Console.WriteLine($"PayPal: Getting status for transaction {transactionId}...");
        // Simulate API call to PayPal
        return "Pending";
    }
}

public class PaymentProcessor
{
    private PaymentGateway _gateway;

    public PaymentProcessor(PaymentGateway gateway)
    {
        _gateway = gateway;
    }

    public void MakePurchase(decimal amount, string currency, string cardNumber, string expiry, string cvv)
    {
        Console.WriteLine("\n--- Making Purchase ---");
        if (_gateway.ProcessPayment(amount, currency, cardNumber, expiry, cvv))
        {
            string transactionId = Guid.NewGuid().ToString();
            _gateway.LogTransaction(transactionId, "Success", amount);
            Console.WriteLine($"Purchase successful! Transaction ID: {transactionId}");
        }
        else
        {
            Console.WriteLine("Purchase failed.");
        }
    }
}

public class Program
{
    public static void Main(string[] args)
    {
        PaymentGateway stripe = new StripeGateway("sk_test_stripe123");
        PaymentProcessor processor1 = new PaymentProcessor(stripe);
        processor1.MakePurchase(100.50m, "USD", "1111222233334444", "12/25", "123");

        PaymentGateway paypal = new PayPalGateway("pp_api_key_xyz");
        PaymentProcessor processor2 = new PaymentProcessor(paypal);
        processor2.MakePurchase(50.00m, "EUR", "5555666677778888", "01/24", "456");
    }
}
```

Is example mein, `PaymentGateway` ek abstract class hai jo sabhi payment gateways ke liye common operations define karti hai aur `GatewayName` aur `ApiKey` jaisi common properties ko initialize karne ke liye ek constructor provide karti hai. Har specific gateway (jaise `StripeGateway`, `PayPalGateway`) `PaymentGateway` se inherit karti hai aur abstract methods ke liye apna implementation provide karti hai.

### When to use an Interface:

Interface ka use tab karein jab:

*   **Aap dissimilar classes ke liye ek contract define karna chahte hain:** Agar classes unrelated hain lekin unhe common functionalities ka ek set provide karna hai, toh interface ideal hai. Yeh specify karta hai ki ek class ko *kya karna chahiye*, bina yeh bataye ki *kaise* karna chahiye.
*   **Aapko type ki multiple inheritance support karne ki zaroorat hai:** Ek class multiple interfaces implement kar sakti hai, jisse use various contracts se behaviors inherit karne ki permission milti hai. (C# classes se implementation ki multiple inheritance support nahi karta).
*   **Aap loose coupling achieve karna chahte hain:** Interfaces loose coupling ko promote karti hain, aapko ek abstraction ke against program karne ki permission deti hain bajaye ek concrete implementation ke. Yeh aapke code ko zyada flexible aur test karne mein aasan banata hai.
*   **Aapke paas "can-do" relationship hai:** Class kuch *kar sakti hai* (jaise, ek `Car` `IDriveable` *ho sakti hai*).
*   **Aap ek aisa behavior define kar rahe hain jise alag-alag types mein plug kiya ja sakta hai:** Jaise, `IComparable`, `IEnumerable`, `IDisposable` common interfaces hain jo specific behaviors define karti hain.

**Real-time C# Example (Interface - Notification Service):**

Ek notification service par vichar karein jise various channels jaise Email, SMS, ya Push Notifications ke through messages send karne ki zaroorat hai. Yeh channels alag-alag hain lekin notification send karne ka common behavior share karte hain. Iske liye ek interface perfect hai.

```csharp
using System;
using System.Collections.Generic;

// Interface for a generic notification sender
public interface INotificationSender
{
    void SendNotification(string recipient, string message);
    string GetSenderType();
}

// Concrete implementation for Email notifications
public class EmailSender : INotificationSender
{
    private string _smtpServer;
    private string _fromAddress;

    public EmailSender(string smtpServer, string fromAddress)
    {
        _smtpServer = smtpServer;
        _fromAddress = fromAddress;
        Console.WriteLine($"EmailSender initialized (SMTP: {_smtpServer}, From: {_fromAddress})");
    }

    public void SendNotification(string recipient, string message)
    {
        Console.WriteLine($"Sending Email to {recipient} from {_fromAddress} via {_smtpServer}: ");
        Console.WriteLine($"  Subject: Notification");
        Console.WriteLine($"  Body: {message}");
        // Simulate email sending logic
    }

    public string GetSenderType()
    {
        return "Email";
    }
}

// Concrete implementation for SMS notifications
public class SmsSender : INotificationSender
{
    private string _smsGatewayUrl;

    public SmsSender(string smsGatewayUrl)
    {
        _smsGatewayUrl = smsGatewayUrl;
        Console.WriteLine($"SmsSender initialized (Gateway: {_smsGatewayUrl})");
    }

    public void SendNotification(string recipient, string message)
    {
        Console.WriteLine($"Sending SMS to {recipient} via {_smsGatewayUrl}: ");
        Console.WriteLine($"  Message: {message}");
        // Simulate SMS sending logic
    }

    public string GetSenderType()
    {
        return "SMS";
    }
}

// Notification service that uses any INotificationSender
public class NotificationService
{
    private List<INotificationSender> _senders;

    public NotificationService()
    {
        _senders = new List<INotificationSender>();
    }

    public void AddSender(INotificationSender sender)
    {
        _senders.Add(sender);
        Console.WriteLine($"Added {sender.GetSenderType()} sender.");
    }

    public void SendBroadcast(string message, string recipient)
    {
        Console.WriteLine($"\n--- Sending Broadcast to {recipient} ---");
        foreach (var sender in _senders)
        {
            sender.SendNotification(recipient, message);
        }
    }
}

public class Program
{
    public static void Main(string[] args)
    {
        NotificationService service = new NotificationService();

        EmailSender email = new EmailSender("smtp.example.com", "no-reply@example.com");
        service.AddSender(email);

        SmsSender sms = new SmsSender("https://sms.gateway.com/api");
        service.AddSender(sms);

        service.SendBroadcast("Your order #123 has been shipped!", "user@example.com");
        service.SendBroadcast("Your account balance is low.", "+15551234567");
    }
}
```

Is example mein, `INotificationSender` notifications send karne ke liye contract define karta hai. `EmailSender` aur `SmsSender` alag-alag classes hain jo is interface ko implement karti hain, har ek apni tarah se notifications send karti hai. `NotificationService` kisi bhi class ke saath kaam kar sakti hai jo `INotificationSender` ko implement karti hai, loose coupling aur flexibility demonstrate karte hue. Yeh naye notification channels (jaise `PushNotificationSender`) ko `NotificationService` ko modify kiye bina easily add karne ki permission deta hai.

## References

- [Microsoft Docs: C# Programming Guide](https://docs.microsoft.com/en-us/dotnet/csharp/programming-guide/)
- [GeeksforGeeks: C# Tutorials](https://www.geeksforgeeks.org/c-sharp-tutorials/)
- [Stack Overflow](https://stackoverflow.com/)
