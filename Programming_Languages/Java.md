---
tags:
  - Programming_Languages
---
# Core principles
## Class
### Attributes
### Methods
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
_JVM stands for Java Virtual Machine_
It is the engine that takes our Java code and turns it into something the computer can execute.
### JRE
_JRE stands for Java Runtime Environment_
Includes the [[#JVM]] and libraries to run Java applications. When we install Java, we are essentially getting the JRE.
### JDK
_JDK stands for Java Development Kit_
Adds tools for development. Extends [[#JRE]]. For example, it includes [[#javac]], the [[Programming Languages#Debugger|debugger]] and other utilites to write Java code.
