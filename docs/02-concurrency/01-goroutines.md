# Goroutines

[Take me to the lab!](https://kodekloud.com/topic/lab-go-routine/)

Help for the [VSCode editor](https://github.com/kodekloudhub/community-faq/blob/main/docs/vscode-tips.md).

1.  <details>
    <summary>What should be the minimum size of the channel in the program to prevent any deadlock error?</summary>

    ```go
    package main

    import (
        "fmt"
        "sync"
    )

    func main() {
        var wg sync.WaitGroup
        wg.Add(2)
        ch := make(chan int, <size>)
        go produce(ch, &wg)
        wg.Wait()
    }

    func produce(ch chan int, wg *sync.WaitGroup) {
        for i := 10; i <= 100; i += 10 {
            ch <- i
        }
        fmt.Println("Exiting Produce")
        close(ch)
        go consume(ch, wg)
        wg.Done()
    }

    func consume(ch chan int, wg *sync.WaitGroup) {
        for val := range ch {
            fmt.Println("Received: ", val)
        }
        fmt.Println("Exiting Consume")
        wg.Done()
    }
    ```

    * 10
    * 5
    * 9
    * 15

    <details>
    <summary>Reveal</summary>

    > 10

    * Look at the `for` loop in the `produce` function. This will iterate 10 times, placing 10 values into the channel before any are read back from the channel.
    * If the buffer is less than 10, then the channel insert within the `for` loop will block.
    * Since no other code is running at this point, it is a deadlock.

    </details>
    </details>

1.  <details>
    <summary>What would be the output for the following code?</summary>

    ```go
    package main

    import "fmt"

    func consume(ch chan int) {
        for {
            select {
            case num := <-ch:
                fmt.Printf("%d ", num)
                break
            }
        }
    }

    func main() {
        ch := make(chan int)
        go consume(ch)

        for i := 0; i < 5; i++ {
            ch <- i
        }

    }
    ```

    (TODO - Await clickup resolution)

    * 0
    * Nondeterministic
    * 0 1 2 3 4 5
    * 0 1 2 3

    <details>
    <summary>Reveal</summary>

    > 0 1 2 3 4

    * Channels operate as first-in first-out queues
    * The `consume` goroutine is started before we start writing to the channel, so it will block until a value is ready.
    * We are only using the one channel so the `consume` function will read out the numbers in the order they were inserted.

    </details>
    </details>

1.  <details>
    <summary>What should be the order of statements to prevent the error in the below code?</summary>

    ```go
    package main

    import (
        "fmt"
        "sync"
        "time"
    )

    func main() {
        var wg sync.WaitGroup
        // Line 1
        go func() {
            time.Sleep(time.Millisecond)
            fmt.Println("Inside Goroutine")
            // Line 2
        }()
        // Line 3
    }
    ```

    * wg.Add(1)<br/>wg.Done()<br/>wg.Wait()
    * wg.Add(1)<br/>wg.Add(1)<br/>wg.Done()<br/>wg.Wait()
    * wg.Add(1)<br/>wg.Wait()<br/>wg.Done()
    * wg.Add(2)<br/>wg.Done()<br/>wg.Wait()

    <details>
    <summary>Reveal</summary>

    > wg.Add(1)<br/>wg.Done()<br/>wg.Wait()

    * We can eliminate one possible answer immediately. We have 3 statements to insert, not 4.
    * With wait groups, we need to add the number of routines we are going to wait on *before* we start the goroutines. This means we can eliminate `wg.Add(2)` and  `Line 1` must therefore be `wg.Add(1)`.
    * At the end of a goroutine, use `wg.Done()` to decrement the wait count, so that's `Line 2`.
    * Finally, the function that needs to block until goroutines complete needs to call `wg.Wait()`, so that's `Line 3`.

    </details>
    </details>

1.  <details>
    <summary>Select the correct behavior for the following program:</summary>

    ```go
    package main
    import (
        "sync"
    )
    func main() {
        ch := make(chan int)
        var wg sync.WaitGroup
        wg.Add(1)
        go func() {
            ch <- 1     // Line 10
            wg.Done()   // Line 11
        }()
        wg.Wait()
        close(ch)
        <-ch
    }
    ```

    * The program runs successfully
    * The program consists of syntax error on line 11
    * The program creates a panic situation on line 10
    * The program ends in a deadlock, causing a fatal error

    <details>
    <summary>Reveal</summary>

    > The program ends in a deadlock, causing a fatal error

    * The wait group `Add`, `Done` and `Wait` all appear to be in the right places as per the previous question.
    * However the goroutine is writing to an unbuffered channel. Since nothing is reading that channel, `ch <- 1` will block, and the goroutine will not reach `wg.Done()`
    * Deadlock will occur when `main` reaches `wg.Wait()` as both it and the goroutine are blocked.

    We could fix this by buffering the channel:

    ```go
    ch := make(chan int, 1)
    ```

    </details>
    </details>

1.  <details>
    <summary>Why does the following program fail?</summary>

    ```go
    package main

    import (
        "fmt"
        "sync"
    )

    func main() {
        var c chan int
        var wg sync.WaitGroup
        wg.Add(2)
        go func() {
            c <- 1
            wg.Done()
        }()

        go func() {
            val := <-c
            fmt.Println(val)
            wg.Done()
        }()
        wg.Wait()
    }
    ```

    * Send on a "nil" channel is blocked.
    * Inconsistent use of wait groups inside the "go-routines".
    * Receive on a "nil" channel is blocked.
    * Sending to and receiving from a "nil" channel blocks forever.

    <details>
    <summary>Reveal</summary>

    > Sending to and receiving from a "nil" channel blocks forever.

    * Both goroutines are blocked because the channel `c` is `nil`
    * `wg.Done()` is never called in either routine
    * `wg.Wait()` causes a deadlock.

    We could fix this by initializing the channel. It doesn't need buffering because the second goroutine will read the value inserted by the first, and both will get to `wg.Done()`

    ```go
    var c = make(chan int)
    ```

    </details>
    </details>

1.  <details>
    <summary>What happens when you write to an unbuffered, closed channel?</summary>

    * Block until something is read from the channel
    * Hang forever
    * Works
    * Panic Situation

    <details>
    <summary>Reveal</summary>

    > Panic Situation

    </details>
    </details>

1.  <details>
    <summary>What happens if you read from an unbuffered, closed channel?</summary>

    * Panic situation
    * Works
    * Returns zero value
    * Block until something is written to the channel

    <details>
    <summary>Reveal</summary>

    > Returns zero value

    * Zero being whatever that means for the type associated with the channel, i.e. `0` for all numeric types, `""` for strings, `false` for booleans etc.

    </details>
    </details>

1.  <details>
    <summary>Select all the correct options for a nil channel.</summary>

    1. Read operation blocks forever.
    1. Write operation blocks forever.
    1. Read operations works and returns a zero value.
    1. Close operation creates a panic situation.

    <details>
    <summary>Reveal</summary>

    > A, B, D

    * We know A and B are true from Q5.
    * If A is true, then C cannot possibly be true.

    </details>
    </details>




