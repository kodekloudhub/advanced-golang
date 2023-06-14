# Goroutines 2

[Take me to the lab!](https://kodekloud.com/topic/lab-2-go-routine/)

Help for the [VSCode editor](https://github.com/kodekloudhub/community-faq/blob/main/docs/vscode-tips.md).

1.  <details>
    <summary>Complete the code below to create two goroutines that will run concurrently using a WaitGroup...</summary>

    The first goroutine should print the numbers 1 to 5, and the second goroutine should print the letters a to e. The main function should wait for both goroutines to finish before exiting.

    A Go file is located at `/root/code/code01` directory. Navigate to it in the Explorer pane and open it.

    Expected output

    Either
    ```
    a
    b
    c
    d
    e
    1
    2
    3
    4
    5
    ```

    Or
    ```
    1
    2
    3
    4
    5
    a
    b
    c
    d
    e
    ```



    <details>
    <summary>Reveal</summary>

    ```go
    package main

    import (
        "fmt"
        "sync"
    )

    func main() {
        var wg sync.WaitGroup
        // Add 2 routines to our wait group
        wg.Add(2)
        // Create first goroutine here
        // Pass address of waitgroup
        go printNumbers(&wg)
        // Create second goroutine here
        go printLetters(&wg)
        // Wait for both goroutines to finish here
        wg.Wait()
    }

    // Modify this function signature to
    // accept a pointer to WaitGroup
    func printNumbers(wg *sync.WaitGroup) {
        for i := 1; i <= 5; i++ {
            fmt.Println(i)
        }
        // Signal done
        wg.Done()
    }

    // Similarly this
    func printLetters(wg *sync.WaitGroup) {
        // 97 thru 102 are ASCII codes for a..e
        for i := 97; i <= 102; i++ {
            // string() of an integer treats
            // the int as an ASCII code
            fmt.Println(string(i))
        }
        // Signal done
        wg.Done()
    }
    ```

    </details>
    </details>

1.  <details>
    <summary>Which of the following is NOT a correct statement about the Golang runtime scheduler?</summary>

    * It is a preemptive scheduler
    * It can move goroutines between OS threads as needed
    * It uses a round-robin algorithm to schedule goroutines
    * It uses a priority-based algorithm to schedule goroutines
    * It allows goroutines to voluntarily yield their time slice

    <details>
    <summary>Reveal</summary>

    > It uses a round-robin algorithm to schedule goroutines

    </details>
    </details>

1.  <details>
    <summary>What would be the output of the code here ?</summary>

    ```go
    package main

    import "fmt"

    func main() {
        ch := make(chan int, 2)
        ch <- 1
        ch <- 2
        close(ch)
        for n := range ch {
            fmt.Println(n)
        }
    }
    ```

    * 1 2
    * 1
    * 2
    * 1<br/>2

    <details>
    <summary>Reveal</summary>

    > 1<br/>2

    * The channel is buffered for 2 elements, therefore both writes to the channel succeed without blocking.
    * The channel is closed, but we can still read any remaining values.
    * Finally range over the channel to receive the two values, and emit with `Println`, which means there will be a newline between `1` and `2`.

    </details>
    </details>

1.  <details>
    <summary>Complete the code here, for the program to work successfully and give the following response with no error:</summary>

    A Go file is located at `/root/code/code04` directory. Navigate to it in the Explorer pane and open it.

    Expected output:

    ```
    sent job 1
    sent job 2
    sent job 3
    sent all jobs
    received job 1
    received job 2
    received job 3
    received all jobs
    ```

    <details>
    <summary>Reveal</summary>

    We only need to add a single line beneath the comment `your code goes here`

    ```go
    close(jobs)
    ```

    * `jobs` is a buffered channel large enough to hold 5 items
    * We send it 3 jobs.
    * The goroutine will receive and print them, then it will block waiting for another job. Meanwhile `main` is waiting to receive from `done` channel, therefore we get a deadlock.
    * By closing the `jobs` channel, the goroutine is unblocked and receives `false` for `more`, so proceeds to write to `done` channel. This is received by `main` and the program completes.

    </details>
    </details>

1.  <details>
    <summary>In this question, we are working on a project that involves processing a large amount of data in parallel using goroutines...</summary>

     We want to use channels to communicate between the goroutines and ensure that all goroutines have finished processing before the program exits.


    Complete the code, so that the program runs successfully without any errors.

    A Go file is located at `/root/code/code05` directory.

    Expected Output:

    ```
    sent job 1
    received job 1
    received job 2
    sent job 2
    received result 2
    received result 4
    received job 3
    received result 6
    sent job 3
    ```

    <details>
    <summary>Reveal</summary>

    * We know that the last line of all three goroutines should be `wg.Done()`, as 3 jobs have been initially added to the WaitGroup.
    * For the two routines that are *writing* to channels, we need to close the channel they are writing to when done writing, which gives us

    ```go
    package main

    import (
        "fmt"
        "sync"
    )

    func main() {

        var wg sync.WaitGroup
        wg.Add(3)
        jobs := make(chan int)
        results := make(chan int)

        go func() {
            for j := range jobs {
                fmt.Println("received job", j)
                result := j * 2
                results <- result
            }
            // your code goes here
            close(results)
            wg.Done()
        }()

        go func() {
            for j := 1; j <= 3; j++ {
                jobs <- j
                fmt.Println("sent job", j)
            }
            // your code goes here
            close(jobs)
            wg.Done()
        }()

        go func() {
            for r := range results {
                fmt.Println("received result", r)
            }
            // your code goes here
            wg.Done()
        }()

        wg.Wait()
    }

    ```

    </details>
    </details>

1.  <details>
    <summary>Which of the following statements is true about the behavior of a channel in Go when it is closed and has no more values to receive?</summary>

    * A receive operation on a closed channel will always return the zero value of the channel's type
    * A receive operation on a closed channel will always return the default value of the channel's type
    * A receive operation on a closed channel will always return an error
    * A receive operation on a closed channel will always block indefinitely

    <details>
    <summary>Reveal</summary>

    > A receive operation on a closed channel will always return the zero value of the channel's type

    Arguably you can say that the zero value and the default value are the same thing. Either way, it's the same value you get if you declare a variable of the given type using `var` without an initializer.

    </details>
    </details>

1.  <details>
    <summary>Guess the output(s) of the following Go code:</summary>

    ```go
    package main

    import (
        "fmt"
        "sync"
    )

    func main() {
        var wg sync.WaitGroup
        wg.Add(2)
        go func() {
            fmt.Println("Goroutine 1")
            wg.Done()
        }()
        go func() {
            fmt.Println("Goroutine 2")
            wg.Done()
        }()
        wg.Wait()
        fmt.Println("Done")
    }
    ```

    1.  ```
        Goroutine 2
        Goroutine 1
        Done
        ```
    1.  ````
        Goroutine 1
        Goroutine 2
        Done
        ```
    1.  ```
        Goroutine 1
        Done
        Goroutine 2
        ```
    1.  ```
        Goroutine 2
        Done
        Goroutine 1
        ```


    <details>
    <summary>Reveal</summary>

    > A and B are correct.

    * We can eliminate C and D due to the fact that both goroutines must complete before `wg.Wait()` returns, meaning that `Done` will always be printed last.
    * A and B are both correct since the execution of goroutines is non-deterministic, so it is possible for either of the two goroutines to execute first. You may find if you run the program you appear to consistently get one result, however that could change if run on a different computer.

    </details>
    </details>

