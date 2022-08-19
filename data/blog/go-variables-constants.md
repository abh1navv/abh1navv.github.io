---
title: 'Variables and Constants in Go'
date: '2022-08-20'
tags: ['go', 'golang']
draft: false
summary: 'Let's look at the different ways to define variables and constants in Go'
image: '/static/images/article-images/gopher.jpg'
---

In Go, there are many ways to define variables. This is because the designers wanted to make the language as flexible as possible.
The decision to use which one is usually based on the scope of the variable, and it's intended usage.
We will talk about variable scoping side by side while we discuss the different ways to define variables.

## 1. Local Scope Variables 

Just like other languages, local variables are defined inside functions. Further, there is a block scope such that variables defined inside a block (if, for, switch, etc.) are not visible outside that block.

### 1.1 Shorthand Declaration

The easiest way to define a local variable is to use the shorthand := syntax. It only requires you to specify the variable name and the value.

This is the preferred way to define a small scope variable that:
- can immediately be assigned a value
- is not used for a long time

The variable type is inferred from the value assigned to it. This is known as type inference.

Let's see an example:
```go
func main() {
    // Shorthand declaration
    a := "Hello"
    b := getName()
    fmt.Println(a, b) // Hello World
}

func getName() string {
    return "World"
}
```

### 1.2. Long Declaration - var and const
As the scope of usage increases beyond a few lines, it's recommended to use the long declaration syntax.
It requires the use of the 'var' keyword to define the variable name and type or the 'const' keyword to define a constant.

The syntax to define a constant or a variable is 
-> [keyword] [name] [type] = [value].
We will read this as "define a [variable/constant] named [name] of type [type] with value [value]".

Let's check the const declaration first.
Here are a few points to keep in mind:
- Constants cannot be reassigned.
- Constants need to be assigned a literal value at the time of declaration. 
- We cannot use function calls or variables as values.
- Declaring the type is optional, as it is always inferred from the value assigned to it.

```go
func main() {
    const a = "Hello"
    const b string = "World"
    fmt.Println(a, b) // Hello World
}
```

Now let's see how to define variables using var. 

```go
func main() {
    var a = "Hello"
    var b string = getName()
    fmt.Println(a, b) // Hello World
}

func getName() string {
    return "World"
}
```

As we can see with variable 'a', when assigning a value at the time of declaration, the type is inferred from the value, and we don't need to specify it.
The recommendation here is to declare the type unless you have a good reason not to (for example, when assigning a literal value).
This makes it easier to know the type without having to check the type of the assigned variable or function call.


### 1.3. Declaration without Assignment

Just like other languages, we can declare a variable without assigning a value to it. There are multiple ways to do this.
This is the preferred way to declare variables which are intended to be used for a long time.

```go
func main() {
    var a, b string
    var c int
    a = "Hello"
    b = getName()
    c = 10
    fmt.Println(a, b, c) // Hello World 10
}

func getName() string {
    return "World"
}
```

If there are multiple variables of the same type, we can declare them in a single line using the comma operator.

There are a few important points to keep in mind about variable declaration:
1. The type always needs to be specified. 
2. Each variable defined needs to be used later on.If you forget to use a variable, the compiler will give you an error. This has been designed to prevent you from forgetting to use a variable.
3. No variable is null. Each declared variable is automatically assigned a "zero" value of the type specified. For example, string variables are initialized to the empty string "" and integer variables are initialized to 0.

### 1.4. Grouped Variable Declaration
In addition to these ways, we can also use a grouped declaration to declare multiple variables at once. 

```go
func main() {
    var (
        a, b string
        c int
    )

    a = "Hello"
    b = "World"
    c = 10
    fmt.Println(a, b, c) // Hello World 10
}
```

We may also use the shorthand declaration syntax to declare multiple variables at once.

```go
func main() {
    a, b, c := "Hello", "World", 10
    fmt.Println(a, b, c) // Hello World 10
}
```

Grouping variable declarations is a good way to reduce the number of lines of code needed to declare multiple variables.
It is also a good way to group variables which have similar usage. It can also be used to group all variables which are used in a single function to make it easier to understand the code.

## 2. Global Variables

Variables which are defined outside the functions are called global variables. Let's talk a little about access rules before we see the syntax. 

### 2.1. Access Rules
Go has only two types of accesses to global variables and functions:
1. Public access - Variables and functions that start with an upper case letter are public (automatically exported by the package) and can be used anywhere in the program.
2. Package access - Variables and functions that start with a lower case letter are private and can only be used inside the package where they are defined.

If you're not familiar with packages in Go, you can think of a package as a collection of files which have the same package name in the first line of the file.

### 2.2. Global Variable Declaration

Let's look at the syntax to declare a global variable or constant.

```go
package main

const a = "Hello"
const b string = "World"

const PI = 3.14

const (
    aa = "Hello"
    bb string = "World"
)

var c int
var d string

var (
    e = "Hello"
    f string = "World"
    g int
)
```

A few important points to keep in mind:
1. Constants are defined with the same rules as they would be inside a function.
2. The constant PI starts with a capital letter and hence can be accessed outside the package using "main.PI". Others are lower case and can only be accessed inside the package.
3. Grouping is allowed for global variables and constants. 
4. Defining the type of variable is optional if the value assigned to it is a literal value. Assigning the value is optional but then the type must be specified.
5. Shorthand declaration is not allowed for global variables and constants.

---