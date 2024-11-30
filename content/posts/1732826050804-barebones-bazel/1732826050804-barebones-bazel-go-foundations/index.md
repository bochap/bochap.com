---
slug: 2024-12-02-barebones-bazel-go-foundations
title: "Barebones Bazel: Go Foundations"
date: 2024-12-02
draft: false
description: "Easily build Go projects with Bazel! This guide covers workspace setup, dependencies, packages, testing, and running your app. Perfect for Bazel beginners."
tags: ["Go", "Bazel", "Build", "Bzlmod", "Packages", "Testing"]
series: ["barebones-bazel"]
series_order: 1
seriesOpened: true
---
## Barebones Bazel: Go Foundations

This comprehensive guide will walk you through the process of building a Go project with Bazel, a powerful open-source build tool. Whether you're a beginner or an experienced Go developer, this guide will equip you with the knowledge and skills to leverage Bazel's capabilities effectively

### Why Bazel?

Many new projects avoid Bazel, intimidated by its perceived complexity. However, adopting Bazel early can pave the way for a smoother, more efficient build process as your project expands. This series provides a gentle introduction to Bazel, using a small Go project with Protocol Buffers to illustrate its benefits. These include:

* **Versatility:** Bazel effortlessly handles a variety of languages and platforms—Go, JavaScript/TypeScript, Protobuf, and more—streamlining your build process with a single, unified tool.

* **Speed and Efficiency:** Bazel optimizes build times by intelligently caching build artifacts and only rebuilding components that have been modified, saving valuable development time.

* **Reproducibility:** Bazel ensures consistent build outputs, regardless of the development environment, guaranteeing reliability and eliminating inconsistencies across different machines.

* **Scalability:** Bazel is engineered to handle massive projects with millions of lines of code, making it an ideal choice for projects with ambitious growth plans.

* **Extensibility:** Bazel's flexible architecture allows you to extend its capabilities and adapt it to your project's unique needs by adding support for new languages and platforms.

This series will guide you through the practical application of Bazel, demonstrating how to harness its power to build, test, and manage your Go projects effectively.

### Setting Up

Before we begin, ensure you have both Go (version 1.22 or higher) and Bazel (version 7.0 or higher) installed on your system. You can find detailed installation instructions on the official Go and Bazel websites.

### Requirements

* Go version 1.22 or higher. As of this writing, the latest version is 1.23. Keep in mind that each major Go release is supported until two newer major releases are available. For the most up-to-date information on Go's release policy, refer to the [official documentation](https://go.dev/doc/devel/release#policy).
* Bazel version 7.0 or higher

### Go Basics: A Single-File Binary

Let's start with the basics:

1. **Create the Go Project Folder:** Create a new directory `go-basics` for the project

2. **Create a Bazel Workspace:** Create an empty `WORKSPACE.bazel` file in `go-basics`. This file signals to Bazel that the directory is a Bazel workspace.
 
3. **Manage External Dependencies:** Create a `MODULE.bazel` file to specify your project's external dependencies.
   
    <details open>
    <summary>Details of MODULE.bazel</summary>
        
    ```python
    bazel_dep(name = "rules_go", version = "0.50.1")

    go_sdk = use_extension("@rules_go//go:extensions.bzl", "go_sdk")

    go_sdk.download(version = "1.22.9")
    ```

    This snippet, written in Bazel's Skylark language which uses a Python-like syntax, manages the external dependencies required for our Go project. Let's break down what each part does:

    * `bazel_dep`: This is a built-in Bazel function that tells Bazel to fetch an external dependency.

        * `name = "rules_go"`: This specifies the name of the dependency, which in this case is the `rules_go` repository. This repository provides the necessary rules for Bazel to build Go projects.

        * `version = "0.50.1"`: This specifies the version of `rules_go` to use.

    * `go_sdk = use_extension("@rules_go//go:extensions.bzl", "go_sdk")`: This line loads a `go_sdk` extension from the `rules_go` repository. Extensions are a way to add new functionality to Bazel. The `go_sdk` extension provides functions for managing the Go SDK.

    * `go_sdk.download(version = "1.22.9")`: This line uses the download function from the `go_sdk` extension to download the Go SDK version 1.22.9. This ensures that Bazel uses a specific Go version for building the project, maintaining consistency and avoiding potential compatibility issues.

    In essence, this Skylark code ensures that Bazel has the necessary tools and dependencies to build our Go project correctly. It fetches the Go rules, loads the Go SDK extension, and downloads the specified Go SDK version. This setup streamlines the build process and ensures that the project builds consistently with the desired Go version.
    </details>

4. **Create the Go Project Logic:** Create a `main.go` file that will contain our simple Go code.

    <details open>
    <summary>Details of main.go</summary>
    
    ```go
    package main

    import "fmt"

    func main() {
        fmt.Println("Hello, Bazel!")
    }
    ```
    
    </details>
        
5. **Create the Go Project Build Configuration:** Create the `BUILD.bazel` file that provides the instructions for Bazel to build the Go code into an executable binary. Here's a breakdown:

    <details open>
    <summary>Details of BUILD.bazel</summary>
    
    ```python
    load("@rules_go//go:def.bzl", "go_binary")
    
    go_binary(
        name = "main",
        srcs = ["main.go"],
    )
    ```
 
    The `BUILD.bazel` file written is written in Bazel's Skylark language, is the control center for building our Go project. Let's break down what each part does:

      * `load("@rules_go//go:def.bzl", "go_binary")`: This line imports the go_binary rule from the @rules_go repository. Think of rules as Bazel's toolbox, providing predefined functions for building specific types of outputs. The go_binary rule, as its name implies, instructs Bazel to create an executable binary from our Go source code.

      * `go_binary(...)`: This is where we define the specifics of the binary we want to build.

        * `name = "main"`: This assigns the name "main" to our binary. This name serves as an identifier within the Bazel environment and will also be the name of the final executable file generated by the build process.

        * `srcs = ["main.go"]`: This line specifies the source files that Bazel will use to build the binary. In this case, it's main.go, the heart of our Go project.

    This concise BUILD.bazel file acts as a blueprint for Bazel, clearly outlining how to build the main executable. By specifying the source files and the go_binary rule, we provide Bazel with all the information it needs to correctly compile and link the Go code, resulting in a functional executable binary ready for use.

    </details>

6. **Project Structure:** Your project directory should have the following structure:

    ```bash
    .
    └── go-basics
        ├── BUILD.bazel
        ├── MODULE.bazel
        ├── WORKSPACE.bazel        
        └── main.go
    ```

7. **Building with Bazel:** In your terminal, navigate to the project folder and build the binary using:

    ```bash
    bazel build //:main
    ```

    Bazel will handle the dependencies and compilation, placing the output in the `bazel-bin` directory.

8. **Running the Binary:** To run your binary, execute:

    ```bash
    bazel run //main
    ```

    You should see "Hello, Bazel!" printed in your terminal.

9.  **Changes made:** The changes made for Go Basics change be found in the commit for commit [ff1f82](https://github.com/bochap-learning/barebones-bazel/commit/ff1f8279627bdee5f37041ea1cff96d769d9bf89)

10. **Summary:** Let's recap what we've done:

   * We've established a Bazel workspace by creating a `WORKSPACE.bazel` file, signaling to Bazel that this directory houses a Bazel project.
   * We then defined external dependencies, including Go rules and the Go SDK version, within a `MODULE.bazel` file.
   * Finally, we instructed Bazel to build an executable binary from our `main.go` source code by creating a `BUILD.bazel` file and employing the `go_binary` rule to define a build target named "main".
   * Bazel simplifies the build process by managing all the underlying details, ensuring your Go code is compiled correctly.

### Go Level Up: Modules and Testing

Let's expand our knowledge to build more complex Go projects that include modules and testing with Bazel.

1. **Create the Go Project:** Create a new directory `go-level-up` for your project.

2. **Create the Bazel Workspace and Manage External Dependencies:** Follow steps 2 and 3 in [Go Basics: A Single-File Binary](#go-basics-a-single-file-binary) to create `WORKSPACE` and `MODULE.bazel`.

3. **Create the `go-level-up` Module:** Open your terminal, navigate to your module directory (`go-level-up`), and create the module using the following command:

    ```bash
    bazel run @rules_go//go:go -- mod init github.com/bochap-learning/barebones-bazel/go-level-up
    ```

    > **_NOTE:_** To avoid potential inconsistencies between your project's Go version and the one used by Bazel, use the Go SDK that Bazel installs.

4. **Create the Currency Package:** Create the directory `currency`.

5. **Create the Currency Logic:** Create the file `converter.go` inside `go-level-up/currency`

    <details open>
    <summary>Details of converter.go</summary>
    
    ```go
    package currency
    
    import (
        "fmt"
        "math/big"
    )
    
    type Converter struct {
        conversionRates map[string]map[string]*big.Rat
    }
    
    func NewConverter(conversionRates map[string]map[string]*big.Rat) (Converter, error) {
        if len(conversionRates) == 0 {
            return Converter{}, fmt.Errorf("rates cannot be nil or empty")
        }
        return Converter{
            conversionRates: conversionRates,
        }, nil
    }

    func (c Converter) Convert(from string, to string, amount *big.Rat) (
        *big.Rat,
        error,
    ) {
        toRate, ok := c.conversionRates[from]
        if !ok {
            return nil, fmt.Errorf("from: %s currency not found", from)
        }
        rate, ok := toRate[to]
        if !ok {
            return nil, fmt.Errorf("to: %s currency not found", to)
        }
        result := new(big.Rat).Mul(amount, rate)
        return result, nil
    }    
    ```
    
    </details>

6. **Create the Currency Tests:** Create the file `converter_test.go` inside `go-level-up/currency`

    <details open>
    <summary>Details of converter_test.go</summary>
    
     ```go
     package currency

     import (
         "math/big"
         "reflect"
         "testing"
     )

     func getValidConverter() (Converter, error) {
         rates := map[string]map[string]*big.Rat{
             "USD": {"JPY": big.NewRat(100, 1)},
             "JPY": {"USD": big.NewRat(1, 100)},
         }
         return NewConverter(rates)
     }

     func TestValidNewConverter(t *testing.T) {
         target, err := getValidConverter()
         if err != nil {
             t.Fatalf("expect error: nil, got: %v", err)
         }
         if reflect.DeepEqual(target, Converter{}) {
             t.Fatal("expect converter: non nil, got: nil")
         }
     }

     func TestInvalidNewConverter(t *testing.T) {
         testCases := []struct {
             description string
             rates       map[string]map[string]*big.Rat
         }{
             {
                 description: "nil rates",
                 rates:       nil,
             },
             {
                 description: "empty rates",
                 rates:       map[string]map[string]*big.Rat{},
             },
         }
         for _, testCase := range testCases {
             t.Run(testCase.description, func(t *testing.T) {
                 target, err := NewConverter(testCase.rates)
                 if err == nil {
                     t.Fatal("expect error: non nil, got: nil")
                 }
                 if !reflect.DeepEqual(target, Converter{}) {
                     t.Fatalf("expect converter: nil, got: %v", target)
                 }
             })
         }
     }

     func TestValidConvert(t *testing.T) {
         target, _ := getValidConverter()
         testCases := []struct {
             description string
             from        string
             to          string
             amount      *big.Rat
             expect      *big.Rat
         }{
             {
                 description: "from: USD, to: JPY, amount: 1",
                 from:        "USD",
                 to:          "JPY",
                 amount:      big.NewRat(1, 1),
                 expect:      big.NewRat(100, 1),
             },
             {
                 description: "from: JPY, to: USD, amount: 100",
                 from:        "JPY",
                 to:          "USD",
                 amount:      big.NewRat(100, 1),
                 expect:      big.NewRat(1, 1),
             },
         }
         for _, testCase := range testCases {
            t.Run(testCase.description, func(t *testing.T) {
                got, err := target.Convert(testCase.from, testCase.to, testCase.amount)
                if err != nil {
                    t.Fatalf("expect error: nil, got: %v", err)
                }
                if !reflect.DeepEqual(testCase.expect, got) {
                    t.Fatalf("expect: %v, got: %v to be equal", testCase.expect, got)
                }
            })
         }
     }

     func TestInvalidConvert(t *testing.T) {
         target, _ := getValidConverter()
         testCases := []struct {
             description string
             from        string
             to          string
             amount      *big.Rat
         }{
             {
                 description: "from: EUR, to: JPY, amount: 1 to fail",
                 from:        "EUR",
                 to:          "JPY",
                 amount:      big.NewRat(1, 1),
             },
             {
                 description: "from: JPY, to: EUR, amount: 100 to fail",
                 from:        "JPY",
                 to:          "EUR",
                 amount:      big.NewRat(100, 1),
             },
         }
         for _, testCase := range testCases {
            t.Run(testCase.description, func(t *testing.T) {
                got, err := target.Convert(testCase.from, testCase.to, testCase.amount)
                if err == nil {
                    t.Fatal("expect error: non nil, got: nil")
                }
                if got != nil {
                    t.Fatalf("expect: nil, got: %v", got)
                }
            })             
         }
     }
     ```
    
    </details>

7. **Create the Client Package:** Create the directory `client` inside `go-level-up`.
   
8.  **Create the Main:** Create the file `main.go` inside `go-level-up/client` with the following content:
    
    <details open>
    <summary>Details of main.go</summary>
    
    ```go
    package main

    import (
        "flag"
        "fmt"
        "math/big"
        "os"

        "github.com/bochap-learning/barebones-bazel/go-level-up/currency"
    )

    func main() {
        from := flag.String("from", "USD", "From currency")
        to := flag.String("to", "JPY", "To currency")
        amount := flag.Float64("amount", 1, "Amount")
        flag.Parse()
        rates := map[string]map[string]*big.Rat{
            "USD": {"JPY": big.NewRat(100, 1)},
            "JPY": {"USD": big.NewRat(1, 100)},
        }
        converter, err := currency.NewConverter(rates)
        if err != nil {
            fmt.Fprintf(os.Stderr, "Error: %v\n", err)
            os.Exit(1)
        }
        ratAmount := new(big.Rat)
        ratAmount.SetFloat64(*amount)
        converted, err := converter.Convert(*from, *to, ratAmount)
        if err != nil {
            fmt.Fprintf(os.Stderr, "Error: %v\n", err)
            os.Exit(1)
        }
        result, isPrecised := converted.Float64()
        fmt.Printf("Convert amount: %f, from: %s, to: %s returns value: %f and precision: %t\n", *amount, *from, *to, result, isPrecised)
    }
    ```
    
    </details>    

9.  **Create the Currency Library `BUILD` File:** Create the file `BUILD.bazel` inside `go-level-up/currency`

    <details open>
    <summary>Details of BUILD.bazel</summary>
    
    ```python
    load("@rules_go//go:def.bzl", "go_library", "go_test")
    
    go_library(
        name = "converter",
        srcs = ["converter.go"],
        importpath = "github.com/bochap-learning/barebones-bazel/go-level-up/currency",
        visibility = ["//visibility:public"],
    )

    go_test(
        name = "converter_test",
        srcs = ["converter_test.go"],
        embed = [":converter"]        
    )
    ```
    
    This `BUILD.bazel` file, written in Bazel's Skylark language, orchestrates the building and testing of our Go code within the `currency` module. Let's break down its components:

    * `load("@rules_go//go:def.bzl", "go_library", "go_test")`: This line imports the necessary rules from the `@rules_go` repository for building and testing Go code. It brings in `go_library` for building Go libraries and `go_test` for defining Go tests.

    * `go_library(...)`: This block defines a Go library.

      * `name = "converter"`: This assigns the name "converter" to our library. This name acts as an identifier within the Bazel environment, allowing other parts of the project to reference and depend on this library.

      * `srcs = ["converter.go"]`: This specifies the source files that constitute the library. In this case, it's `converter.go`, which contains the core logic for our currency conversion functionality.

      * `importpath = "github.com/bochap-learning/barebones-bazel/go-level-up/currency"`:  This attribute explicitly sets the Go import path for the library. This is crucial for ensuring that other Go code can correctly import and use this library. It establishes the library's identity within the Go ecosystem.

      * `visibility = ["//visibility:public"]`: This attribute controls the visibility of the library, determining which other parts of the project (or even external projects) can depend on it. Setting it to `//visibility:public` makes this library accessible from anywhere, enabling broader code reuse and modularity.

    * `go_test(...)`: This block defines a Go test target.

      * `name = "converter_test"`: This gives the name "converter_test" to our test target. This name helps identify the test within the Bazel environment.

      * `srcs = ["converter_test.go"]`: This specifies the source files containing the test code. Here, it's `converter_test.go`, which houses the test functions that will exercise the currency conversion logic.

      * `embed = [":converter"]`: This line is crucial for linking the test target with the library it's meant to test. It tells Bazel to embed the `converter` library (identified by `:converter`) within the `converter_test` target, ensuring that the test code can access and utilize the library's functionality.

    This `BUILD.bazel` file effectively sets up the building blocks for our Go code. It defines a Go library containing the core logic and a corresponding test target to verify its correctness. This structured approach ensures that Bazel can correctly compile and link the code, facilitating both development and testing processes.

    </details>

10. **Build the Currency Library:** Open your terminal, navigate to your module directory (`go-level-up`), and run the following command:
 
    ```bash
    bazel build //go-level-up/currency:converter
    ```

11. **Run the Currency Tests:** Open your terminal, navigate to your module directory (`go-level-up`), and run the following command:

    ```bash
    bazel test //go-level-up/currency:converter_test --cache_test_results=no
    ```

12. **Create the Currency Client `BUILD` File:** Create the file `BUILD.bazel` inside `go-level-up/client`

    <details open>
    <summary>Details of BUILD.bazel</summary>
    
    ```python
    load("@rules_go//go:def.bzl", "go_binary")

    go_binary(
        name = "service",
        srcs = ["main.go"],
        deps = [
            "//go-level-up/currency:converter"
        ]
    )
    ```
    
    This BUILD.bazel file, crafted in Bazel's Skylark language, guides Bazel in building the Go code within the client module into an executable binary. Let's break it down

    * `load("@rules_go//go:def.bzl", "go_binary")`: This line imports the `go_binary` rule from the `@rules_go` repository. Rules are Bazel's pre-defined building blocks, providing instructions for specific outputs. The `go_binary` rule instructs Bazel to create an executable binary from Go source code.

    * `go_binary(...)`: This defines the specifics of the binary.

      * `name = "service"`: This assigns the name "service" to the binary, acting as an identifier within Bazel and the name of the final executable file.

      * `srcs = ["main.go"]`: This specifies the source files used to build the binary. In this case, it's `main.go`, containing the entry point and core logic for the client application.

      * `deps = ["//go-level-up/currency:converter"]`: This crucial line defines the dependencies for the binary. It tells Bazel that the `service` binary depends on the `converter` library located in the `go-level-up/currency` module. This ensures that Bazel links the `converter` library with the `service` binary during the build process.

    This `BUILD.bazel` file acts as a blueprint for Bazel, outlining how to build the `service` executable. By specifying the source files, dependencies, and the `go_binary` rule, it provides Bazel with the necessary information to correctly compile and link the Go code, resulting in a functional executable binary.

    </details>
        
13. **Build the Currency Client:** Open your terminal, navigate to your module directory (`go-level-up`), and run the following command:

    ```bash
    bazel build //go-level-up/client:service
    ```

14. **Run the Currency Client:** Open your terminal, navigate to your module directory (`go-level-up`), and run the following command:

    ```bash
    bazel run //go-level-up/client:service -- -amount 100 -from JPY -to USD
    ```

15. **Project structure:** Your project directory should have the following structure:

    ```bash
    .
    ├── ...
    ├── MODULE.bazel
    ├── README.md
    ├── WORKSPACE.bazel
    ├── go.mod
    ├── client
    │   ├── BUILD.bazel
    │   └── main.go
    └── currency
        ├── BUILD.bazel
        ├── converter.go
        └── converter_test.go
    ```

16.  **Changes made:** The changes made for Go Level Up change be found in the commit for commit [586cde](https://github.com/bochap-learning/barebones-bazel/commit/586cde8490a709cbba2539c896be6b77ffeec486)

17. **Summary:** This section details how Bazel manages a Go project with multiple modules. It covers:

    * Dependency Management: Leveraging `MODULE.bazel` to declare external dependencies like Go rules and specify the Go SDK version.
    * Build Targets: Utilizing `BUILD.bazel` files in each module (currency and client) to define build targets and dependencies using rules like go_library and go_test.
    * Building and Testing: Employing Bazel commands (bazel build and bazel test) to build the Go libraries and execute tests.
    * This process highlights Bazel's role in orchestrating the build and test process for multi-module Go projects, ensuring consistency and reproducibility.

### Conclusion

This guide provides a solid foundation for building Go projects with Bazel. We've covered the essentials, from setting up a workspace to building and testing Go code. In future articles, we'll delve into more advanced Bazel techniques. Stay tuned!

### Streamlining Bazel Workflows: Useful Commands

Here are some handy commands to boost your efficiency when working with Bazel:

1. **Executing Go Commands within Bazel:** To seamlessly run Go-related commands within your Bazel environment, use the following structure
    
    ```bash
    bazel run @rules_go//go:go -- [Go related comman like mod, get, list]
    ```
    
    This allows you to leverage Go tools like `mod`, `get`, and `list` without leaving the Bazel context.

2. **Unveiling the Bazel Workspace:** To get a comprehensive view of all paths within your Bazel workspace, the `bazel query` command is your ally. For instance, to list all Go packages in your workspace, you would execute:

    ```bazel
    bazel query ...
    ```

    This powerful command provides a clear picture of your project's structure and dependencies, aiding in navigation and analysis.