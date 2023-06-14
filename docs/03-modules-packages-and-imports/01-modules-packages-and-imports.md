# Modules, Packages and Imports


[Take me to the lab!](https://kodekloud.com/topic/lab-modules-packages-and-imports/)

Help for the [VSCode editor](https://github.com/kodekloudhub/community-faq/blob/main/docs/vscode-tips.md).


1.  <details>
    <summary>For the following module setup in your local environment, choose the correct import statement to import the <b>abc</b> package in <b>module_2</b></summary>

    ```
    module_1
    |
    └─── abc
         |
         └─── utils.go

    module_2

    ```

    * `import "../abc"`
    * `import <module path for module_1>/abc`
    * `import "module_1/abc"`
    * `import module_1/abc/utils`

    <details>
    <summary>Reveal</summary>

    > `import <module path for module_1>/abc`

    * Go doesn't support relative imports therefore we always need to use a module path
    * The path will have been determined when you did `go mod init`

    </details>
    </details>


1.  <details>
    <summary>Select the valid import statement in Go.</summary>

    * `import "github/kodekloud/adv-go"`
    * `import "<https://github.com/kodekloud/adv-go>"`
    * `import "github.com/kodekloud/adv-go"`
    * `import "../adv-go"`

    <details>
    <summary>Reveal</summary>

    > `import "github.com/kodekloud/adv-go"`

    * As before, we can't use relative paths so that rules out one answer.
    * There's no syntax involving `<` and `>` so that rules that one out.
    * Module paths look like web URLs (usually to git repos), and `github/` on it's own isn't a valid website hostname, so that rules that one out.

    </details>
    </details>


1.  <details>
    <summary>Given a variable named points, declared outside of all the functions in a file in a package <b>utils</b> inside the module <b>module_1</b>...</summary>

    Select the correct option regarding its accessibility:-

    * It can be accessed from anywhere in `module_1`.
    * It can be anywhere inside `utils` package, not the rest of `module_1`
    * It can be accessed by other packages in `module_1` as long as they import `utils` package.
    * It can be accessed by any application that imports `module_1`.

    <details>
    <summary>Reveal</summary>

    > It can be anywhere inside `utils` package, not the rest of `module_1`

    * Even though `utils` is inside (or beneath) `module1`, it is still a package in its own right.
    * Because `points` does not start with an uppercase letter, it is not exported outside of the package that declares it, i.e. `utils`

    </details>
    </details>


1.  <details>
    <summary>Imagine a scenario where we need to import <b>mymodule.com/utils</b> <span>(http://mymodule.com/utils)</span> package, but the install location is different...</summary>

    Select the correct option to go with in such cases:-

    * Install the package using the correct "go get" command.
    * Changing the import path while installing the package.
    * Using a proxy.
    * Use the replace directive in `go.mod` file.

    <details>
    <summary>Reveal</summary>

    > Use the replace directive in `go.mod` file.

    * For the case where the package path doesn't match the install location (maybe it was moved), we have the `replace` directive to tell the package loader where to look.

    </details>
    </details>


1.  <details>
    <summary>Given these files, make appropriate changes in utils.go for the code in main.go to work.</summary>

    `main.go` file is located at the `/root/code/gofolder` directory.

    `utils.go` file is located at the `/root/code/gofolder/temp/utils` directory.

    <details>
    <summary>Reveal</summary>

    * There is actually one change to make in each file
    * Know that for something to be visbile from outside of its own package, the identifier must begin with an uppercase letter.

    Thus the solution is to change `calcSquare` to `CalcSquare` in both files. Do this, then run it to test...

    ```bash
    cd /root/code/gofolder
    go run main.go
    ```

    </details>
    </details>


1.  <details>
    <summary>What information does go.mod file include?</summary>

    * Go version
    * Dependencies needed
    * Module replacement information
    * All of the above

    <details>
    <summary>Reveal</summary>

    > All of the above

    </details>
    </details>


1.  <details>
    <summary>Select the correct syntax for replacing the installation location for a package: -</summary>

    * `go mod edit --require example.com/temp=/Users/priyadav/GolandProjects/examples/temp`
    * `go mod edit -replace example.com/temp=/Users/priyadav/GolandProjects/examples/temp`
    * `go mod replace example.com/temp=/Users/priyadav/GolandProjects/examples/temp`
    * `go mod edit example.com/temp=/Users/priyadav/GolandProjects/examples/temp`

    <details>
    <summary>Reveal</summary>

    > `go mod edit -replace example.com/temp=/Users/priyadav/GolandProjects/examples/temp`

    You can find the right answer from the terminal...

    ```
    go help mod
    ```

    You notice there's no `replace` command, but there is an `edit` command, so

    ```
    go help mod edit
    ```

    And in the sea of output, you find the `-replace` flag. That's what you need.

    </details>
    </details>


