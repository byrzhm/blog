---
title: Getting Started with Go (Golang)
date: 2025-03-01 13:11:00 +0800
categories: [Programming Languages, Go]
tags: [go]     # TAG names should always be lowercase
pin: false
math: false
mermaid: false
---

Go, often referred to as **Golang**, is an open-source programming language developed by Google engineers. It combines the simplicity of syntax similar to Python with the performance and safety of languages like C++. Go is designed for building fast, reliable, and scalable software, making it ideal for modern applications like cloud services, APIs, and DevOps tools.

In this tutorial, you’ll learn how to set up Go, write your first program, and explore core concepts.

---

## **Step 1: Install Go**

### **Download and Install**
1. Visit the official Go downloads page: [https://go.dev/dl/](https://go.dev/dl/).
2. Download the installer for your OS (Windows, macOS, or Linux).

#### **Windows/macOS**
   - Run the downloaded `.msi` (Windows) or `.pkg` (macOS) file and follow the prompts.

#### **Linux**
   - Extract the tarball to `/usr/local`:
     ```bash
     sudo tar -C /usr/local -xzf go$VERSION.$OS-$ARCH.tar.gz
     ```
   - Add Go to your `PATH` by adding this line to `~/.bashrc` or `~/.zshrc`:
     ```bash
     export PATH=$PATH:/usr/local/go/bin
     ```
   - Run `source ~/.bashrc` (or restart your terminal).

---

### **Verify Installation**
Open a terminal and run:
```bash
go version
```
You should see output like `go version go1.21.0 linux/amd64`.

---

## **Step 2: Set Up Your Workspace**
Go uses **modules** for dependency management. Create a project directory anywhere on your system (no need for a strict `GOPATH` structure in newer Go versions).

1. Create a project folder:
   ```bash
   mkdir my-go-project && cd my-go-project
   ```
2. Initialize a Go module:
   ```bash
   go mod init example.com/myproject
   ```

---

## **Step 3: Write Your First Program**
Create a file `hello.go` with the following code:

```go
package main

import "fmt"

func main() {
    fmt.Println("Hello, World!")
}
```

### **Explanation**
- `package main`: Declares this file as part of the `main` package (required for executables).
- `import "fmt"`: Imports the `fmt` package for input/output operations.
- `func main()`: The entry point of the program.

---

## **Step 4: Run the Program**
In the terminal, run:
```bash
go run hello.go
```
Output:
```
Hello, World!
```

---

## **Step 5: Learn Basic Syntax**

### **Variables**
Use `var` or shorthand `:=` for type inference:
```go
var name string = "Alice"
age := 30 // Type inferred as int
```

### **Functions**
Define a function with parameters and return types:
```go
func add(a int, b int) int {
    return a + b
}
```

### **Control Structures**
#### **If-Else**
```go
if x > 10 {
    fmt.Println("Large")
} else {
    fmt.Println("Small")
}
```

#### **For Loop**
Go only has a `for` loop (no `while` keyword):
```go
for i := 0; i < 5; i++ {
    fmt.Println(i)
}
```

---

## **Step 6: Concurrency with Goroutines and Channels**
Go’s standout feature is built-in concurrency using **goroutines** (lightweight threads) and **channels** (for communication).

### **Goroutine Example**
```go
package main

import (
    "fmt"
    "time"
)

func printNumbers() {
    for i := 1; i <= 3; i++ {
        time.Sleep(1 * time.Second)
        fmt.Println(i)
    }
}

func main() {
    go printNumbers() // Runs concurrently
    fmt.Println("Started goroutine")
    time.Sleep(4 * time.Second) // Wait for goroutine to finish
}
```

### **Channels Example**
```go
func main() {
    ch := make(chan string)

    go func() {
        ch <- "Message from goroutine"
    }()

    msg := <-ch
    fmt.Println(msg) // Output: Message from goroutine
}
```

---

## **Step 7: Dependency Management**
Go Modules handle dependencies. To add a third-party package:
1. Import it in your code:
   ```go
   import "github.com/gorilla/mux"
   ```
2. Run `go mod tidy` to download and install the package.

---

## **Next Steps**
1. Explore the standard library: [https://pkg.go.dev/std](https://pkg.go.dev/std).
2. Build a REST API using `net/http` or frameworks like Gin.
3. Practice concurrency patterns (e.g., worker pools).
4. Read *"The Go Programming Language"* book or the official [Tour of Go](https://go.dev/tour/).

---

## **Conclusion**
You’ve installed Go, written a basic program, and explored its core features. Go’s simplicity and performance make it a powerful tool for modern development. Happy coding! 🚀
