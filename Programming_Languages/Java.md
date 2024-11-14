---
tags:
  - Programming_Languages
---
# Core principles
## Class
### Child-Parent
A class can be the child of some parent class, called _superclass_.
The child class inherits the parent class, but also can have other [[#Attributes]] and [[#Methods]], because the child class is more specific and detailed (fine-grained), in a lower level.
```Java
class Dog extends Animal { //Means that class Dog is child of class Animal
	...
}
```
### Abstract
An **abstract class** is a class from where we cannot create an object. It is meant to serve as a blueprint for other classes.
It can contain both [[#Methods#Abstract|abstract methods]] and normal [[#Methods|methods]] (i.e. with implementation).
```Java
abstract class Animal {
	abstract void sound();
	void sleep() {
		System.out.println("Sleeping");
	}
}
```
The abstract class serves for defining a [[#Class#Child-Parent|child]] class from the abstract one. Then, in that child class, we **must** define the superclass-abstract methods implementation (in the example above, `sound()`):
```Java
class Dog extends Animal {
	void sound() {
		System.out.println("Bark");
	}
}
```
- The child class could be another abstract class! In that case, we are not forced to define the method's implementations of the superclass
### Attributes
### Methods
#### Abstract
An **abstract method** is a method without body implementation.


## Object
An instantiation of a [[#Class]]. In other words, an object is the result of providing a value for each [[#Class#Attributes|attribute]] of a Class. For example:
```Java
blue_car = new Car(colour = "blue")
```
## javac
The Java [[Programming Languages#Compiler|compiler]].
## Servlet
_Belongs to [[#Java EE]]_
A Java program implemented in [[Internet#Server|server]] which handle [[Internet#Client|client]] requests.
Designed to respond various types of requests such as [[HTTP#Message format|HTTP requests]].
In terms of Java, it is a [[#Class]] which contains the method bodies to process requests and return results to [[Internet#Client|clients]].
### Container
Whenever we perform a [[HTTP#Message format|HTTP request]] against some URL, that URL is mapped to a Servlet by the [[#Servlet]] Container.
For example, we can associate several [[Internet#Web server#Endpoint|endpoints]] to one servlet, in such way that that servlet would process the received request to that endpoint.
For example: For the endpoint `/users` , we will define the following Servlet:
```Java
@WebServlet(name = "ExampleServlet", urlPatterns = "/users")
	public class ExampleServlet extends HttpServlet {
	
	protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
		String param1 = request.getParameter("param1");
		String param2 = request.getParameter("param2");
		// Process the request and generate a response
		response.getWriter().write("Processed param1: " + param1 + ", param2: " + param2);
	}
}
```
### Lifecycle
Managed by the servlet [[#Container]]
A servlet goes through the following stages:
1. `init()`: the servlet initializes when the servlet [[#Container]] detects a request to some servlet. Then, some initial setup, such as [[Databases]] connections are performed at this stage
2. `doPost()`, `doGet()`, `doPut()`...: once the servlet has been initialized, these methods are used to process the [[HTTP#Message format|HTTP request]]
3. `destroy()`: once the servlet has completed its task and no longer needed, it can be destroyed to free resources
### Hello world Servlet Example
```Java
import java.io.*;
import javax.servlet.*;
import javax.servlet.http.*;

public class HelloWorld extends HttpServlet {
    private String message;

    public void init() throws ServletException {
        message = "Hello, World!";
    }

    public void doGet(HttpServletRequest request, HttpServletResponse response)
		throws ServletException, IOException {
	        response.setContentType("text/html");
	        PrintWriter out = response.getWriter();
	        out.println("<h1>" + message + "</h1>");
	    }

    public void destroy() {
        // Clean up resources if needed
    }
}
```
## Java EE
_Java EE stands for Java Enterprise Edition_
_Also known as **Jakarta**_
It is the enterprise Java environment, that provides us the utilities to make websites and powerful server applications.
It is an extension of [[#Java SE]], so it includes everything that belongs to Java SE plus the utilities for developing websites and powerful server applications.
## Java SE
_Java SE stands for Java Standard Edition_
_Also known as **Java Platform**_
It is the basic Java environment, that allows us to write the most standard programs with Java. Provides the essential components [[#JVM]], [[#JRE]] and [[#JDK]].
### JVM
_JVM stands for Java Virtual Machine, and it is a program_
It is the engine that takes our Java code and turns it into something the computer can execute.
Thanks to JVM, we can run Java code on many different [[Operating System|Operating Systems]].
JVM is in charge of handling the memory usage (e.g. in [[C]] or [[C++]] we have to free or allocate [[Operating System#Secondary memory (Disk)|memory]] usage when we are coding, but in Java this process is made by JVM). This process is called [[#Garbage Collection]].
#### Garbage Collection
_Refer to [Java Garbage Collection Basics](https://www.oracle.com/webfolder/technetwork/Tutorials/obe/java/gc01/index.html)_
When Java programs run on the JVM, [[#Object|objects]] are created on the heap. Over time, some objects become unused or unreferenced. The garbage collector identifies these unused objects and deletes them to free up memory. The process involves three main phases:
1. **Marking**: Identifying which objects are in use and which are not. It takes a long time to perform this action (and especially if there are many objects in heap), as it requires to scan the Heap and determine which 
2. **Sweeping**: Removing the unreferenced objects
3. **Compacting**: Moving the remaining objects to make memory allocation easier
##### Hotspot Architecture
Allows dynamic optimizations while the Java code is running.
![[HotSpotJVM_Architecture.png]]
The components related to performance are: _The Heap_, _The JIT Compiler_ and _The Garbage Collector_, and we will have to tune them for better performance.
- **The Heap**: where the objects are stored. We can tune its size
- **Garbage Collector**: manages objects stored in the Heap. We can tune it by selecting the appropiate [[#Garbage Collectors|garbage collector type]]
- **JIT Compiler**: rarely needs tunning
###### Garbage Collectors
Java provides several types of garbage collectors, each suited for different scenarios:
1. **Serial GC**: Suitable for small applications running on single-threaded environments. It performs garbage collection serially in one thread
2. **Parallel GC**: Uses multiple threads for minor garbage collection and is suitable for applications with medium to large data sets
4. **CMS (Concurrent Mark Sweep) GC**: Minimizes pause times by performing most of the garbage collection work concurrently with application threads
5. **G1 (Garbage First) GC**: Designed for multi-threaded applications with large heap sizes. It identifies regions with the most garbage and performs garbage collection on those regions first
6. **ZGC**: Suitable for applications requiring low latency and large heap sizes. It performs all garbage collection operations concurrently with the application
We can choose which one to use for each [[#JVM Generations|JVM generation]].
##### JVM Generations
When we create an [[#Object|object]] in Java, it is stored in the Heap (i.e. in [[Operating System#Main memory (RAM)|RAM memory]]). To do so, JVM allocates some memory to store the recently created object.
After some elapsed time, Garbage Collector performs the [[#Garbage Collection|Garbage Collection steps (i.e. the Collection)]]: _Marking_, _Sweeping_ and _Compacting_ for the current allocated objects in memory.
If there are many objects, the Garbage Collection time can remarkably increase. 
From the early Garbage Collection mechanisms, It was shown that most objects are short-lived (i.e. just a few bytes are really in use by the Java code over time), and only a few of them are really used (referenced) after a long time since the execution start.
That's why we don't have to check whether we are using those surviving objects: the oldest objects are very likely to be used, so we can save time and check if we are using them less frequently than youngest objects (i.e. which have just been created). Therefore, JVM Generations were implemented to differentiate between young and old objects and apply the Garbage Collection steps with different frequencies (or even apply a different [[#Garbage Collectors|collector type]]) depending on the age of those objects.
##### Performance metrics
We are interested in [[#Responsiveness]] and [[#Throughput]].
###### Responsiveness
Responsiveness refers to how quickly an application or system responds with a requested piece of data. Examples include:
- How quickly a desktop UI responds to an event
- How fast a website returns a page
- How fast a database query is returned
###### Throughput
Throughput focuses on maximizing the amount of work by an application in a specific period of time. Examples of how throughput might be measured include:
- The number of transactions completed in a given time
- The number of jobs that a batch program can complete in an hour
- The number of database queries that can be completed in an hour
### JRE
_JRE stands for Java Runtime Environment_
Includes the [[#JVM]], the core [[#Class|classes]] of the Java platform and libraries to run Java applications. When we install Java, we are essentially getting the JRE.
### JDK
_JDK stands for Java Development Kit_
Adds tools for development. Extends [[#JRE]]. For example, it includes [[#javac]], the [[Programming Languages#Debugger|debugger]] and other utilites to write Java code.
### Java API
The JDK and the JRE share the Java Application Programming Interfaces ([Java API](http://docs.oracle.com/javase/7/docs/api/)). The Java API is a collection of prepackaged libraries developers use to create Java applications. The Java API makes development easier by providing the tools to complete many common programming tasks including string manipulation, date/time processing, networking, and implementing data structures (e.g., lists, maps, stacks, and queues).