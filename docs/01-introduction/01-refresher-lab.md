# Refresher Lab

[Take me to the lab!](https://kodekloud.com/topic/refresher-lab/)

Help for the [VSCode editor](https://github.com/kodekloudhub/community-faq/blob/main/docs/vscode-tips.md).

1.  <details>
    <summary>In this question, we are building a simple calculator that can perform addition, subtraction, multiplication, and division operations.</summary>

    Complete the code in the function `calculate` to return a slice consisting of 4 elements [ `sum` of a and b, `difference` of a and b, `product` of a and b, `quotient` on dividing a by b]

    A Go file is located at `/root/code/calculator/` directory. Navigate to this file in the Explorer pane and open it.

    Expected Output

    ```
    [30 10 200 2]
    [770 630 49000 10]
    ```

    <details>
    <summary>Reveal</summary>

    ```go
    package main

    import "fmt"

    func calculate(a int, b int) []float64 {
        // Make the results slice,
        // large enough for the four results
        results := make([]float64, 4)

        // First element is sum
        results[0] = float64(a+b)
        // Second element is difference
        results[1] = float64(a-b)
        // Third element is product
        results[2] = float64(a*b)
        // Final element is quotient
        // Note the separate conversions to float64
        // of a and b here. If the division is not
        // exact, we would expect a floating point result!
        results[3] = float64(a)/float64(b)

        return results
    }

    func main() {
        fmt.Println(calculate(20, 10))
        fmt.Println(calculate(700, 70))
    }
    ```

    Now run the program

    ```bash
    cd /root/code/calculator
    go run main.go
    ```

    Note that it could also be done this way, but if you know how big you need a slice to be, it is more efficient to allocate first it using `make`, as grow operations on slices consume processor time.

    ```go
    import "fmt"

    func calculate(a int, b int) []float64 {
        // Make the results slice,
        results := []float64{}

        // Append the calculations in order
        results = append(results, float64(a+b))
        results = append(results, float64(a-b))
        results = append(results, float64(a*b))
        results = append(results, float64(a)/float64(b))

        return results
    }

    func main() {
        fmt.Println(calculate(20, 10))
        fmt.Println(calculate(700, 70))
    }
    ```

    </details>
    </details>

1.  <details>
    <summary>We are building a program to keep track of the number of visitors to a website.</summary>

    We need to store the number of active visitors in a variable and update it each time a new visitor arrives or an old visitor leaves the website.

    A Go file is located at `/root/code/visitor` directory. Navigate to this file in the Explorer pane and open it.

    Expected output
    ```
    2
    ```

    <details>
    <summary>Reveal</summary>

    Much easier than the last question :smile:

    ```go
    package main

    import "fmt"

    // Declare variable activeUserCount
    var activeUserCount int

    func entry() {
        // Hint: you can use the "++" operator to increment a variable by 1
        activeUserCount++
    }

    func exit() {
        // Hint: you can use the "--" operator to decrement a variable by 1
        activeUserCount--
    }

    func main() {
        entry()
        entry()
        exit()
        exit()
        entry()
        entry()
        fmt.Println(activeUserCount)
    }
    ```

    Run it...

    ```bash
    cd /root/code/visitor
    go run main.go
    ```

    </details>
    </details>

1.  <details>
    <summary>We are building a software for a store that sells three products: apples, bananas, and oranges...</summary>

    We need to write a function that takes the name of a product and its price as arguments and returns the price of the product with a discount applied. The discount should be `10%` for `apples` and `20%` for `bananas`. `Oranges` do not have a discount.

    A Go file is located at `/root/code/product` directory. Navigate to this file in the Explorer pane and open it.

    Expected output (TODO - Await clickup resolution)

    ```
    90
    100
    80
    80
    100
    ```

    <details>
    <summary>Reveal</summary>

    ```go
    package main

    import "fmt"

    func discountedPrice(product string, price float64) float64 {
        // Ideal opportunity for a switch!
        switch product {
        case "apple", "apples":
            // Note case for singlular and plural
            return price * 0.9
        case "banana", "bananas":
            return price * 0.8
        default:
            // The default case is no discount
            return price
        }
    }

    func main() {
        fmt.Println(discountedPrice("apples", 100))
        fmt.Println(discountedPrice("orange", 100))
        fmt.Println(discountedPrice("bananas", 100))
        fmt.Println(discountedPrice("banana", 100))
        fmt.Println(discountedPrice("oranges", 100))
    }
    ```

    Run it...

    ```bash
    cd /root/code/product
    go run main.go
    ```

    </details>
    </details>

1.  <details>
    <summary>In this question, we are building a tool that analyzes the frequency of words in a given text</summary>

    You need to implement a function `wordFrequency` that receives a string and returns a map with the frequency of each word in the string.

    A Go file is located at /root/code/frequency directory.

    Expected output

    ```
    map[The:1 brown:1 dog:1 fox:1 jumps:1 lazy:1 over:1 quick:1 the:1]
    ```

    <details>
    <summary>Reveal</summary>

    The clue is that the`strings` package has been imported, so it's likely there will be a [function that can help us](https://pkg.go.dev/strings#Fields) with splitting the sentence into words!

    ```go
    package main

    import (
        "fmt"
        "strings"
    )

    func wordFrequency(text string) map[string]int {
        // Create a map for the results
        results := make(map[string]int)
        // Break up the sentence into separate words
        words := strings.Fields(text)
        // Iterate the words, counting them into the map
        for _, word := range words {
            // Have we seen it?
            _, ok := results[word]
            if ok {
                // Yes - increment word count
                results[word]++
            } else {
                // No - new word
                results[word] = 1
            }
        }

        return results
    }

    func main() {
        text := "The quick brown fox jumps over the lazy dog"
        fmt.Println(wordFrequency(text))
    }
    ```

    Run it...

    ```bash
    cd /root/code/frequency
    go run main.go
    ```


    </details>
    </details>

1.  <details>
    <summary>We are working on a project for a grocery store. The store has a list of items that are currently in stock, and we need to implement a function that will return a list of items that are in a given price range.</summary>

    A Go file is located at `/root/code/fruit` directory.

    Expected Output:

    ```
    [{Apple 0.5} {Banana 0.25} {Orange 0.75}]
    [{Apple 0.5} {Orange 0.75}]
    [{Orange 0.75} {Pineapple 1.5}]
    ```

    <details>
    <summary>Reveal</summary>

    ```go
    package main

    import "fmt"

    type Item struct {
        Name  string
        Price float64
    }

    func getItemsInPriceRange(items []Item, minPrice, maxPrice float64) []Item {
        // Create empty slice for results
        results := []Item{}
        // Iterate items testing the price
        for _, item := range items {
            if item.Price >= minPrice && item.Price <= maxPrice {
                // Within range - append to results
                results = append(results, item)
            }
        }
        return results
    }

    func main() {
        items := []Item{
            {Name: "Apple", Price: 0.5},
            {Name: "Banana", Price: 0.25},
            {Name: "Orange", Price: 0.75},
            {Name: "Pineapple", Price: 1.5},
        }

        fmt.Println(getItemsInPriceRange(items, 0.0, 1.0))
        fmt.Println(getItemsInPriceRange(items, 0.5, 1.0))
        fmt.Println(getItemsInPriceRange(items, 0.75, 1.5))
    }
    ```

    Run it...

    ```bash
    cd /root/code/fruit
    go run main.go
    ```

    </details>
    </details>

1.  <details>
    <summary>We are working on a program that allows users to store and retrieve information about their favourite books</summary>

    We have implemented a `Book` struct to store the information for the book.

    You need to implement a function that will change the value of the `Pages` field for a given `Book`.

    Also, make the required changes in the main function.

    A Go file is located at `/root/code/book` directory.

    Expected Output:

    ```
    &{The Great Gatsby F. Scott Fitzgerald 210}
    &{To Kill a Mockingbird Harper Lee 250}
    &{Pride and Prejudice Jane Austen 295}
    ```
    
    <details>
    <summary>Reveal</summary>

    ```go
    package main

    import "fmt"


    type Book struct {
        Title  string
        Author string
        Pages  int
    }

    func updatePages(book *Book, pages int) {
        // Note we have a pointer to Book
        // No need to return anything,
        // just set the new value
        book.Pages = pages
    }

    func main() {

        /*
            Create 3 Book Structs with the following data:

            Book 1:
            Title: "The Great Gatsby"
            Author: "F. Scott Fitzgerald"
            Pages: 180

            Book 2
            Title: "To Kill a Mockingbird"
            Author: "Harper Lee"
            Pages: 281

            Book 3
            Title: "Pride and Prejudice"
            Author: "Jane Austen"
            Pages: 279
        */

        // your code for creating struct objects goes here
        // Note creating book *pointers* here on purpose
        book1 := Book{
            Title: "The Great Gatsby",
            Author: "F. Scott Fitzgerald",
            Pages: 180,
        }

        book2 := Book{
            Title: "To Kill a Mockingbird",
            Author: "Harper Lee",
            Pages: 281,
        }

        book3 := Book{
            Title: "Pride and Prejudice",
            Author: "Jane Austen",
            Pages: 279,
        }

        /*
            Update the information for Books as following:

            Book 1: Updates Page Count to 210
            Book 2: Updates Page Count to 250
            Book 3: Updates Page Count to 295

        */

        // your code for function calls to updatePages goes here
        // The function expects a pointer, so no need of &
        updatePages(book1, 210)
        updatePages(book2, 250)
        updatePages(book3, 295)
        /*
            Print all the struct objects
            fmt.Println(book)
        */

        // your code for printing objects goes here
        // The output is expected as &{...} which indicates
        // the need to print pointers, not objects. 
        // This is why we created pointer objects in the
        // first place
        fmt.Println(book1)
        fmt.Println(book2)
        fmt.Println(book3)
    }
    ```

    Run it...

    ```bash
    cd /root/code/book
    go run main.go
    ```


    It could also be done this way by *not* creating pointers initially, and using `&` wherever pointers are required:

    ```go
    package main

    import "fmt"


    type Book struct {
        Title  string
        Author string
        Pages  int
    }

    func updatePages(book *Book, pages int) {
        book.Pages = pages
    }

    func main() {
        book1 := Book{
            Title: "The Great Gatsby",
            Author: "F. Scott Fitzgerald",
            Pages: 180,
        }

        book2 := Book{
            Title: "To Kill a Mockingbird",
            Author: "Harper Lee",
            Pages: 281,
        }

        book3 := Book{
            Title: "Pride and Prejudice",
            Author: "Jane Austen",
            Pages: 279,
        }

        updatePages(&book1, 210)
        updatePages(&book2, 250)
        updatePages(&book3, 295)

        fmt.Println(&book1)
        fmt.Println(&book2)
        fmt.Println(&book3)
    }
    ```

    </details>
    </details>

1.  <details>
    <summary>We are building a program that helps users track their expenses...</summary>

    We need to create a struct called Expense to store information about an individual expense, including the name of the `expense`, the `amount`, and the `date`.

    We need to create a method called `Total` that calculates the total amount spent on expenses.

    Also, we need to create a method called `getName` on Expense struct that returns the name of the Expense.

    A Go file is located at `/root/code/store/` directory.

    Expected Output:

    ```
    120
    Grocery
    ```

    <details>
    <summary>Reveal</summary>

    ```go
    package main

    import "fmt"

    // Declare the Expense struct here
    // Note that in the decalrations of Expense below
    // the fields are not named, so we can call them what we want.
    type Expense struct {
        name string
        amount float32	// Must be float since initializers have .0
        date string
    }

    // Implement the Total method to calculate the total amount spent
    // your code goes here
    // We can tell the signature of this function
    // by how it is called below
    func Total(expenses []Expense) (total float32) {
        for _, expense := range expenses {
            total += expense.amount
        }

        // `total` is implicitly returned here
        return
    }

    // Implement the getName method on the Expense struct here
    // your code goes here
    // This must be a receiver due to how it is called below
    func (e Expense) getName() string {
        return e.name
    }

    func main() {
        expenses := []Expense{
            Expense{"Grocery", 50.0, "2022-01-01"},
            Expense{"Gas", 30.0, "2022-01-02"},
            Expense{"Restaurant", 40.0, "2022-01-03"},
        }

        fmt.Println(Total(expenses))
        fmt.Println(expenses[0].getName())
    }
    ```

    Run it...

    ```bash
    cd /root/code/store
    go run main.go
    ```

    </details>
    </details>

1.  <details>
    <summary>What would be the output of the following code?</summary>

    Given code is written in a Go file called main.go inside `/root/code/struct` directory.

    1.  ```
        Product details:
        5
        700
        Chair
        ```
    1.  ```
        Product details:
        5
        Chair
        700
        ```
    1.  ```
        Product details:
        700
        Chair
        5
        ```
    1.  ```
        Product details:
        Chair
        5
        700
        ```

    <details>
    <summary>Reveal</summary>

    > D

    Run the program

    ```bash
    cd /root/code/struct
    go run main.go
    ```

    However, you should by now know which order the output would be printed by examining the code in `main` :wink:

    </details>
    </details>

1.  <details>
    <summary>How do you create and initialize a pointer in Go?</summary>

    * `p := &i*`
    * `p := new(int)*`
    * `var p **int = i*`
    * `var p = &int(5)`

    <details>
    <summary>Reveal</summary>

    This is currently nonsense

    </details>
    </details>

1.  <details>
    <summary>Select the correct base case for function printSquares</summary>

    for input n -> prints squares for n, n-1, n-2, …… -5

    Example:

    Input: n=2; Output: 4 1 0 1 4 9 16 25

    ```go
    package main

    import "fmt"

    func printSquares(n int) {
        // base case

        fmt.Printf("%d ", n*n)
        printSquares(n - 1)
    }

    func main() {
        printSquares(2)
    }
    ```

    * `if n == 0 { return }`
    * `if n == -6 { return }`
    * `if n == -5 { return }`
    * `if n == 5 { return }`

    <details>
    <summary>Reveal</summary>

    > `if n == -6 { return }`

    This is a repeat question from the basic course. See [question 5 here](https://github.com/kodekloudhub/golang/blob/main/docs/05-using-functions.md/03-recursive-functions.md)

    </details>
    </details>

