# Core Packages

[Take me to the lab!](https://kodekloud.com/topic/lab-core-packages/)

Help for the [VSCode editor](https://github.com/kodekloudhub/community-faq/blob/main/docs/vscode-tips.md).

1.  <details>
    <summary>Given a string str:= "I love football" . Select the correct function call to replace the word I with We.</summary>

    * `ReplaceAll( "I", "We", str)`
    * `ReplaceAll( str, "I", "We")`
    * `ReplaceAll( str, "We", "I")`
    * `ReplaceAll( str, "We", "I", "I love football")`

    <details>
    <summary>Reveal</summary>

    > `ReplaceAll( str, "I", "We")`

    * https://pkg.go.dev/strings#ReplaceAll

    </details>
    </details>

1.  <details>
    <summary>Write a code in main.go file to read the string in a buffer of size 5 at a time and print the output in each iteration.</summary>

    A Go file is located at `/root/code/string` directory.

    <details>
    <summary>Reveal</summary>

    ```go
    package main

    import (
        "fmt"
        "strings"
    )

    func main() {
        reader := strings.NewReader("Let us catch up over a cup of coffee")
        // your code goes here
        buf := make([]byte, 5)
        for {
            n, err := reader.Read(buf)
            fmt.Println(buf[:n], err)
            if err != nil {
                break
            }
        }

    }
    ```

    * The code creates a byte slice of length 5, which will be used as a buffer.
    * The code then enters an infinite loop which repeatedly calls the `Read` method on the `reader` object, passing the buffer as an argument.
    * The `Read` method reads from the input string and fills the buffer with up to 5 bytes of data.
    * It returns the number of bytes read, `n`, and an error, `err`.
    * The code then prints the bytes read from the buffer and the error.
    * If the error is not `nil`, the loop breaks.
    * This will continue until the entire input string has been read and the error returned is `io.EOF`, indicating the end of the input.


    </details>
    </details>

1.  <details>
    <summary>For the following file structure, choose the correct file path function call for path to temp.text.</summary>

    ```
    dir1
    --dir2
    ----dir3
    --------temp.txt
    ```

    1.  ```go
        filepath.Join("dir1", "../dir1/dir2", "dir3", "temp.txt")
        ```
    1.  ```go
        filepath.Join("dir1", "dir2", "dir3", "temp.txt")
        ```
    1.  ```go
        filepath.Join("dir2", "../dir1/dir2", "dir3", "temp.txt")
        ```
    1.  ```go
        filepath.Join("dir2", "../dir1/dir2//", "dir3", "../dir3/temp.txt")
        ```

    (More than one options might be correct)

    * A and B
    * C
    * All of these
    * D
    * B, C and D

    <details>
    <summary>Reveal</summary>

    > All of these

    ```go
    package main

    import (
        "fmt"
        "path/filepath"
    )

    func main() {
        fmt.Println(filepath.Join("dir1", "../dir1/dir2", "dir3", "temp.txt"))
        fmt.Println(filepath.Join("dir1", "dir2", "dir3", "temp.txt"))
        fmt.Println(filepath.Join("dir2", "../dir1/dir2", "dir3", "temp.txt"))
        fmt.Println(filepath.Join("dir2", "../dir1/dir2//", "dir3", "../dir3/temp.txt"))
    }
    ```

    The output for this would be:
    ```
    dir1/dir2/dir3/temp.txt
    dir1/dir2/dir3/temp.txt
    dir1/dir2/dir3/temp.txt
    dir1/dir2/dir3/temp.txt
    ```

    Join is used to construct paths in a portable way. It takes any number of arguments and constructs a hierarchical path from them.

    </details>
    </details>

1.  <details>
    <summary>A text file is given called temp.txt. Write a code in main.go file to open and read from the file in such a way, to produce the following output:</summary>

    ```
    Lorem Ipsum is simply dummy text of the printing a
    nd typesetting industry. Lorem Ipsum has been the
    industry's standard dummy text ever since the 1500
    s, when an unknown printer took a galley of type a
    nd scrambled it to make a type specimen book.
    ```

    NOTE: - The output text should have 50 characters per line!


    A Text file is located at the `/root/code/book` directory with the above content all on one line.

    A Go file is located at the `/root/code/book` directory for you to edit.

    <details>
    <summary>Reveal</summary>

    ```go
    package main

    import (
        "fmt"
        "os"
    )

    func main() {

        // Open the text file
        f, _ := os.Open("temp.txt")
        // make a 50 byte buffer
        b := make([]byte, 50)
        for {
            // Read 50 bytes (size of the buffer determines this)
            n, err := f.Read(b)
            // Convert the buffer to a string and print it,
            // using a slice to cover the number of bytes read
            // Last block will be less than 50 bytes.
            fmt.Println(string(b[0:n]))
            if err != nil {
                // Exit loop because we have reached end of file
                // err will be `io.EOF`
                break
            }
        }
    }
    ```

    </details>
    </details>

1.  <details>
    <summary>In the following program, throw a custom error with the given statement, for the following cases.</summary>

    * If divisor is less than zero: "Division is not supported for negative numbers"
    * If divisor is equal to zero: "Cannot divide by zero"


    A Go file is located at the `/root/code/division` directory for you to edit

    <details>
    <summary>Reveal</summary>

    Complete the `divideNumber` function like this

    ```go
    func divideNumber(m int, n int) (int, error) {
        // your code goes here
        if n < 0 {
            return 0, errors.New("Division is not supported for negative numbers")
        }
        if n == 0 {
            return 0, errors.New("Cannot divide by zero")
        }
        return m / n, nil
    }
    ```

    And add an import for the `errors` package, or you'll get a compiler error.

    </details>
    </details>

1.  <details>
    <summary>What would be the output for the following code?</summary>

    ```go
    package main

    import (
        "fmt"
        "sort"
    )

    func main() {

        var strs = []string{"Apple", "Around", "Armor", "An"}
        sort.Strings(strs)
        fmt.Println(strs)
    }
    ```

    * [An Apple Armor Around]
    * []
    * [Around Armor Apple An]
    * Syntax Error

    <details>
    <summary>Reveal</summary>

    * [An Apple Armor Around]

    * Strings are sorted in ascending dictionary order.

    </details>
    </details>

1.  <details>
    <summary>What does log.Fatal do?</summary>

    * It prints the log and then raises a panic.
    * It exits the program.
    * It prints the log and then safely exits the program
    * It raises a panic.

    <details>
    <summary>Reveal</summary>

    > It prints the log and then safely exits the program

    </details>
    </details>

1.  <details>
    <summary>Solve the syntax errors in main_test.go file, so go test gives a PASS.</summary>

    A Go file is located at the `/root/code/test` directory.

    <details>
    <summary>Reveal</summary>

    ```go
    package main
    import "testing" // <- add this

    // add the required argument "t *testing.T"
    func TestHelloWorld(t *testing.T) {

        actual := HelloWorld() // <- add this
        if actual != "hello world" {
            t.Errorf("expected 'hello world', got '%s'", actual)
        }
    }
    ```

    ```bash
    cd /root/code/test
    go test
    ```

    </details>
    </details>

