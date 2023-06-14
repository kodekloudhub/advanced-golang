# Core Packages 2

[Take me to the lab!](https://kodekloud.com/topic/lab-2-core-packages/)

Help for the [VSCode editor](https://github.com/kodekloudhub/community-faq/blob/main/docs/vscode-tips.md).

1.  <details>
    <summary>Which of the following statement is true about the strings.Contains function in Go?</summary>

    * It returns the length of the input string
    * It returns a boolean value indicating whether the substring was found in the input string
    * It returns the input string with the substring replaced by another string
    * It returns the index of the first occurrence of the substring in the input string

    <details>
    <summary>Reveal</summary>

    > It returns a boolean value indicating whether the substring was found in the input string

    Clue's in the name! It tests whether the substring is contained within the input string.

    </details>
    </details>

1.  <details>
    <summary>We are working on a project that involves processing a large number of text documents. We need to write a function that counts the number of times a specific word appears in a given string.</summary>

    Let's write a function `WordCount` that takes in a string `s` and a string `word` and returns the number of times the word appears in s. The function should be case-sensitive, so `hello` and `Hello` are considered to be different words.

    A Go file is located at `/root/code/alpha` directory for you to edit.
    <details>
    <summary>Reveal</summary>

    ```go
    package main

    import (
    "fmt"
    "strings"
    )

    func WordCount(s string, word string) int {
        // Your code goes here
        return strings.Count(s, word)
    }

    func main() {
        count := WordCount("hello, Hello how have you been in helloworld", "hello")
        fmt.Println(count)
    }
    ```

    * This function uses the strings.Count method to count the number of occurrences of word in `s`.
    * The `Count` method is case-sensitive, so it will correctly count the number of times `word` appears in `s` even if `word` and `s` have different capitalization.
    * The `Count` method recognises word boundaries (whitespace, punctuation, start and end of line) thus it will count `hello,` and ` Hello `, but not the hello in `helloworld`.

    </details>
    </details>

1.  <details>
    <summary>We are working on a project that involves reading data from a text file and processing it. We need to write a function that reads the contents of a text file and returns a slice of strings, where each string represents a line in the file.</summary>

    A text file called data.txt contains the below content inside the /root/code/data directory.

    ```
    Lorem Ipsum is simply dummy text of the printing and typesetting industry.
    Lorem Ipsum has been the industry's standard dummy text ever since the 1500s.
    when an unknown printer took a galley of type and scrambled it to make a type specimen book.
    ```

    Also, a Go file is located inside the directory called `/root/code/data/` for you to edit.

    <details>
    <summary>Reveal</summary>

    * There are a couple of clues in the imports. You're going to need things from [os](https://pkg.go.dev/os) and [bufio](https://pkg.go.dev/bufio) packages

    ```go
    package main

    import (
        "bufio"
        "fmt"
        "os"
    )

    func ReadLines(filename string) ([]string, error) {
        // your code goes here

        // Open the file for reading
        file, err := os.Open(filename)
        if err != nil {
            // If there was an error, return it
            return nil, err
        }

        // Ensure file is closed when the function completes
        defer file.Close()

        // Create a scanner. Scanner will read a file line by line.
        scanner := bufio.NewScanner(file)
        // Declare a slice to hold the lines
        var lines []string

        // Loop until Scan returns false (finished or error)
        for scanner.Scan() {
            // Append next line to the slice
            lines = append(lines, scanner.Text())
        }

        // Check if the scan had an error
        if err := scanner.Err(); err != nil {
            return nil, err
        }

        // Scan was good. Return lines it read
        // and nil to indicate no error.
        return lines, nil
    }

    func main() {
        lines, err := ReadLines("data.txt")
        if err != nil {
            fmt.Println(err)
        } else {
            for _, line := range lines {
                fmt.Println(line)
            }
        }
    }
    ```

    Compile and run

    ```bash
    cd /root/code/data/
    go run main.go
    ```

    </details>
    </details>

1.  <details>
    <summary>We have given a text file called input.txt that contains a list of integers, one per line. Our task is to read the given file, sum the integers, and write the sum to a new file called output.txt within a same directory.</summary>

    A Go file is located at `/root/code/xerox` directory for you to edit.

    Expected Output:

    A file output.txt would be created containing the sum as follows: -

    ```
    160
    ```

    <details>
    <summary>Reveal</summary>

    * There are a couple of clues in the imports. You're going to need things from [os](https://pkg.go.dev/os), [bufio](https://pkg.go.dev/bufio) and [strconv](https://pkg.go.dev/strconv) packages
    * Examine `input.txt`. Note that there is one integer per line, so you will need to read the file line by line as in the previous question.
    * Each line is a string containing a number, therefore to sum the numbers you will need to convert them from string to integer.

    ```go
    package main

    import (
        "bufio"
        "fmt"
        "os"
        "strconv"
    )

    func main() {
        // Open the input file
        inputFile, err := os.Open("input.txt")
        if err != nil {
            // If error, print it and end program
            fmt.Println(err)
            return
        }
        // Ensure the input file is closed at end of function
        defer inputFile.Close()

        // Create a scanner to read the input file
        inputScanner := bufio.NewScanner(inputFile)

        // Initialize the sum to 0
        sum := 0

        // Iterate over the lines of the input file
        for inputScanner.Scan() {
            // Parse the current line as an integer
            n, err := strconv.Atoi(inputScanner.Text())
            if err != nil {
                // If error, print it and end program
                fmt.Println(err)
                return
            }

            // Add the integer to the sum
            sum += n
        }

        // Check for errors while reading the input file
        if err := inputScanner.Err(); err != nil {
            // If error, print it and end program
            fmt.Println(err)
            return
        }

        // Open the output file
        outputFile, err := os.Create("output.txt")
        if err != nil {
            // If error, print it and end program
            fmt.Println(err)
            return
        }

        // Ensure the output file is closed at end of function
        defer outputFile.Close()

        // Write the sum to the output file as a string
        _, err = outputFile.WriteString(strconv.Itoa(sum))

        // You could also do it this way
        //  _, err = fmt.Fprintf(outputFile, "%d", sum)

        if err != nil {
            // If error, print it and end program
            fmt.Println(err)
            return
        }
    }
    ```

    Compile and run

    ```bash
    cd /root/code/xerox
    go run main.go

    cat output.txt
    ```

    </details>
    </details>

1.  <details>
    <summary>In this question, we are building a Go application that involves interacting with a remote API. We want to handle API errors in a consistent and structured way. We have chosen to use the errors package to accomplish this task.</summary>

    Our task is to create a new error type called APIError that implements the error interface.

    The APIError type should have the following fields:

    * Message: A string that describes the error.
    * Code: An integer that represents the error code returned by the API.
    * Details: A map[string]interface{} that contains additional error details.


    We should implement the Error method for the APIError type, which should return the error message. Also, we should implement a function called NewAPIError that creates a new instance of the APIError type.

    A Go file is located at `/root/code/infra` directory for you to edit

    Expected Output:

    ```
    Bad request
    ```

    <details>
    <summary>Reveal</summary>

    ```go
    package main

    import (
        "fmt"
    )

    type APIError struct {
        // your code goes here
        Message string
        Code    int
        Details map[string]interface{}
    }

    func (e *APIError) Error() string {
        // your code goes here
        return e.Message
    }

    func NewAPIError(code int, message string, details map[string]interface{}) error {
        // your code goes here

        // Note that we are creating and returing a pointer to
        // our struct.
        // To implement the error interface, we need to refer to custom
        // errors as pointers or the Error() interface will not be satisfied. Note the receiver above requires pointer.
        return &APIError{
            Message: message,
            Code:    code,
            Details: details,
        }

    }

    func main() {
        err := NewAPIError(400, "Bad request", map[string]interface{}{
            "field": "username",
            "error": "cannot be empty",
        })
        fmt.Println(err.Error())
    }
    ```

    Compile and run

    ```bash
    cd /root/code/infra
    go run main.go
    ```

    </details>
    </details>

1.  <details>
    <summary>We are building a Go application and we want to add logging to help you debug and troubleshoot issues. We have chosen to use the logrus package to accomplish this task.</summary>

    Problem Statement:


    The` logrus` package provides several functions for logging messages at different levels (e.g. `Debug`, `Info`, `Warning`, `Error`).
    Our task is to use the `logrus` package to log messages at different levels. We should create a function called `logMessage` that takes in a level string and a message string as arguments.

    If the level string is `debug`, the function should log the message using the `Debug` function.

    If the level string is `info`, the function should log the message using the `Info` function.

    If the level string is `warning`, the function should log the message using the `Warning` function.

    If the level string is `error`, the function should log the message using the `Error` function.

    If the level string is none of these, the function should log an error message using the `Error` function in the following format: -

    ```
    "Invalid log level: " + level
    ```

    A Go file is located at `/root/code/logrus` directory for you to edit.

    Expected Output:

    ```
    DEBU[0000] This is a debug message
    INFO[0000] This is an info message
    WARN[0000] This is a warning message
    ERRO[0000] This is an error message
    ERRO[0000] Invalid log level: invalid
    ```


    Refer to the [documentation](https://pkg.go.dev/github.com/sirupsen/logrus) for the `logrus` package, where you will find the four functions referred to above.

    <details>
    <summary>Reveal</summary>

    * The solution is a simple `switch` block:

    ```go
    package main

    import log "github.com/sirupsen/logrus"

    // your code goes here
    func logMessage(level string, message string) {
        switch level {
        case "debug":
            log.Debug(message)
        case "info":
            log.Info(message)
        case "warning":
            log.Warning(message)
        case "error":
            log.Error(message)
        default:
            log.Error("Invalid log level: " + level)
        }
    }

    func main() {
        log.SetLevel(log.DebugLevel)
        logMessage("debug", "This is a debug message")
        logMessage("info", "This is an info message")
        logMessage("warning", "This is a warning message")
        logMessage("error", "This is an error message")
        logMessage("invalid", "This is an invalid message")
    }
    ```

    Compile and run

    ```bash
    cd /root/code/logrus
    go run main.go
    ```

    </details>
    </details>

1.  <details>
    <summary>Which of the following is NOT a benefit of writing tests for your Go code?</summary>

    * Tests can help you refactor your code with confidence, knowing that you have a suite of tests to catch any regressions
    * Tests can help ensure that your code is correct and free of bugs
    * Tests can help you optimize your code for better performance
    * Tests can help you understand how your code is supposed to work

    <details>
    <summary>Reveal</summary>

    > Tests can help you optimize your code for better performance

    * There is another part of the `testing` package to use for this, which is `benchmark`

    </details>
    </details>



