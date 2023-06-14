# Modules, Packages and Imports 2


[Take me to the lab!](https://kodekloud.com/topic/lab-2-modules-packages-and-imports/)

Help for the [VSCode editor](https://github.com/kodekloudhub/community-faq/blob/main/docs/vscode-tips.md).


1.  <details>
    <summary>In Go, which of the following statement is true about the visibility of identifiers (such as variables, constants, and functions) within and between packages?</summary>

    * All identifiers are always visible within a package, but only some identifiers are visible from other packages
    * Only exported identifiers are visible within and from other packages
    * Only exported identifiers are visible within a package, but all identifiers are always visible from other packages
    * All identifiers are always visible within a package, but only exported identifiers are visible from other packages

    <details>
    <summary>Reveal</summary>

    > All identifiers are always visible within a package, but only exported identifiers are visible from other packages

    * Whiilst "some" identifiers are visible for other packages, this statement is not clear about what "some" means. It has to be "exported".

    </details>
    </details>

1.  <details>
    <summary>In this question, we are writing a Go package that provides a function for generating random strings. Also, we want to document the function so that users of our package can understand how to use it.</summary>

    A Go file is located at `/root/code/string` directory.

    Complete the above Go code, so the Output for go doc GenerateRandomString is as follows: -

    ```
    ❯ go doc GenerateRandomString
    func GenerateRandomString(length int) string
        GenerateRandomString generates a random string of the given length.
        The string consists of uppercase and lowercase letters and digits.

        Example:

            result := GenerateRandomString(8)
            fmt.Println(result) // "z5F7G9J3"
    ```
    <details>
    <summary>Reveal</summary>

    ```go
    package main
    //
    // ... implementation goes here ...
    //

    // GenerateRandomString generates a random string of the given length.
    // The string consists of uppercase and lowercase letters and digits.
    //	result := GenerateRandomString(8)
    //	fmt.Println(result) // "z5F7G9J3"
    func GenerateRandomString(length int) string {

    }
    ```

    * Know that to get the example, you must insert `TAB` between `//` and the example text.

    </details>
    </details>

1.  <details>
    <summary>In Go, which of the following is NOT a valid version number for a module?</summary>

    * 1.0.0
    * 1
    * 1.0
    * v1.0.0

    <details>
    <summary>Reveal</summary>

    > 1

    * In Go, a module version number consists of at least two non-negative integers separated by a dot.
    * The first integer is the major version, the second integer is the minor version, and additional integers (if present) are the patch version.
    * Valid version numbers include `v1.0.0`, `1.0.0`, `1.0`, and `1.0.1`, but `1` is not a valid version number because it does not have at least two integers separated by a dot.


    </details>
    </details>

1.  <details>
    <summary>Which of the following statements is NOT true about creating a module in Go?</summary>

    * You can use the "go mod download" command to download all of the dependencies for a module
    * You can use the "go mod edit" command to change the dependencies of a module
    * Modules are automatically created whenever you import a package in your Go code
    * To create a new module, you must use the go mod init command and specify the module's path
    * Modules are a new feature in Go that allow developers to manage their dependencies more easily

    <details>
    <summary>Reveal</summary>

    > Modules are automatically created whenever you import a package in your Go code

    * You don't have a module until you've run `go mod init`

    </details>
    </details>




