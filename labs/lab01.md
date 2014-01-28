---
layout: default
title: "Lab 1: Basic Web Service"
---

Getting Started
===============

Start by downloading the following zip files and importing them into Eclipse using **File &rarr; Import... &rarr; General &rarr; Existing Projects into Workspace**:

> [CS496\_Jetty.zip](../assign/CS496_Jetty.zip) <br />
> [CS496\_Lab01.zip](CS496_Lab01.zip)

You should see two projects in your workspace, **CS496\_Jetty** and **CS496\_Lab01**.  You will modify code in the **CS496\_Lab01** project.  The **CS496\_Jetty** project contains the [Jetty](http://www.eclipse.org/jetty/) web server and other useful libraries.

The **edu.ycp.cs496.lab01.main.Main** class has a **main** method which starts the web service, accepting requests on port 8081.

What You Will Learn
===================

Creating web services in Java is easy and fun.

Concept/Background
==================

In this lab, you will implement a simple web service to perform arithmetic operations requested by clients.  This section describes the underlying model and controller classes for the web service.

Model classes
-------------

There are three model classes, **OperationType**, **Operation**, and **Result**.

**OperationType** is an enum type that represents the kinds of arithmetic operations the web service can perform:

    public enum OperationType {
        ADDITION,
        SUBTRACTION,
        MULTIPLICATION,
        DIVISION,
    }

**Operation** is a class representing an operation to be performed, consisting of an **OperationType** value and two operands of type **double**:

    public class Operation {
        private OperationType operationType;
        private double first;
        private double second;
        
        public Operation() {
            
        }
        
        public void setOperationType(OperationType operationType) {
            this.operationType = operationType;
        }
        
        public OperationType getOperationType() {
            return operationType;
        }
        
        public void setFirst(double first) {
            this.first = first;
        }
        
        public double getFirst() {
            return first;
        }
        
        public void setSecond(double second) {
            this.second = second;
        }
        
        public double getSecond() {
            return second;
        }
    }

Note that the **Operation** class is a [POJO](http://en.wikipedia.org/wiki/Plain_Old_Java_Object) data type; POJO stands for "Plain Old Java Object".  Model classes in web services and web applications should generally be POJO types, because they can be easily converted to and from data formats such as [JSON](http://en.wikipedia.org/wiki/JSON).

The **Result** class represents the result of an operation:

    public class Result {
        private double value;
        
        public Result() {
            
        }
        
        public void setValue(double value) {
            this.value = value;
        }
        
        public double getValue() {
            return value;
        }
    }

The **Result** class is also a POJO.

Controllers
-----------

A *controller* is a class which embodies a computation that can be performed using one or more model objects.  Web services and applications should use controllers to perform computations that are requested by clients.  Because a controller class is a problem domain class, it is not dependent on a UI, web framework, etc., and can be tested easily using standard unit tests.

The **PerformOperation** class is a controller which takes an **Operation** and carries out the specified operation:

    public class PerformOperation {
        public double perform(Operation operation) {
            switch (operation.getOperationType()) {
            case ADDITION:
                return operation.getFirst() + operation.getSecond();
            case SUBTRACTION:
                return operation.getFirst() - operation.getSecond();
            case MULTIPLICATION:
                return operation.getFirst() * operation.getSecond();
            case DIVISION:
                return operation.getFirst() / operation.getSecond();
            default:
                throw new UnsupportedOperationException("Unknown operation type: " + operation.getOperationType());
            }
        }
    }

Your Task
=========

Your task is to implement a Java servlet to accept HTTP requests from clients and perform the requested operation.

The servlet will accept requests in two forms as described below.

The example URLs below assume that the web service is reached via the URL

> http://localhost:8081/

The servlet that handles both forms of requests is defined in the class **edu.ycp.cs496.lab01.servlets.Op**, and is accesed via the path **/op** as described below.

First Request Form
------------------

The **Op** servlet should accept **GET** requests where the request is specified by query parameters.  The **type** parameter specifies the **OperationType**, the **first** parameter specifies the first operand, and the **second** parameter specifies the second operand.  The response sent back to the client should have **application/json** as its content type and should contain a **Result** object encoded in JSON format as its body.

Because this servlet accepts **GET** requests and takes its parameters as part of the URL, you can access and test this servlet directly from a web browser.

For example, a request to the URL

> http://localhost:8081/op/?type=ADDITION&first=3&second=4.5

should result in the following JSON data as a response:

    {"value":7.5}

### Hints

You can use the **getParameter** method of the **HttpServletRequest** class to get a query parameter.  For example, to get the operation type:

    String opTypeAsString = req.getParameter("type");

Note that the return value will be **null** if the **type** parameter is not specified.

You can convert a string to an enumeration value using the **valueOf** static method, e.g.

    OperationType opType = OperationType.valueOf(opTypeAsString);

You can use the **Double.parseDouble** static method to convert a string to a **double**:

    String firstAsString = req.getParameter("first");
    double first = Double.parseDouble(firstAsString);

Use the converted query parameters to create an **Operation** object representing the overall request:

    Operation op = new Operation();
    op.setOperationType(...);
    op.setFirst(...);
    op.setSecond(...);

Use a **PerformOperation** controller object to perform the requested operation, producing a **Result** object:

    PerformOperation controller = new PerformOperation();
    Result result = controller.perform(op);

To convert the **Result** object to JSON, use **JSON.getObjectMapper()** to get an **ObjectMapper** method, then use **writeValue** to write the JSON-encoded representation to the response body:

    JSON.getObjectMapper().writeValue(resp.getWriter(), result);

Note that **ObjectMapper** is part of the [Jackson](https://github.com/FasterXML/jackson) library: see the description of [Assignment 1](../assign/assign01.html) for more details.  The idea is that as long as your model classes are POJOs, Jackson can automatically convert between the objects and JSON representations of the objects.

Second Request Form
-------------------

The **Op** servlet should accept **POST** requests with the path **/op** where the body of the request is a JSON-encoded **Request** object, and return a JSON-encoded **Result** object as a result.

To send a **POST** request containing arbitrary data, use the **curl** program.  For example, let's say we save the following JSON data in a text file called **myop.txt**:

    {
        "operationType" : "ADDITION",
        "first" : 3.0,
        "second" : 4.5
    }

We could then use the following **curl** command to submit this request to the web service (run this in Cygwin terminal):

    curl -X POST -d @myop.txt http://localhost:8081/op

The JSON data returned in the body of the response should be

    {"value":7.5}

### Hints

To read the body of the request, parse it as JSON, and create an **Operation** object:

    Operation op = JSON.getObjectMapper().readValue(req.getReader(), Operation.class);

Note that the Jackson library will throw an exception if the request body does not contain valid JSON data, or if the JSON data cannot be converted to an **Operation** object.  Specifically:

* A **JsonParseException** will be thrown if the request body is not syntactically valid JSON data
* An **UnrecognizedPropertyException** will be thrown if the JSON data has an unexpected field

<!-- vim:set wrap: ­-->
<!-- vim:set linebreak: -->
<!-- vim:set nolist: -->
