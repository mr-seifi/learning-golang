# Go Commands

- The **go run** command builds the binary, exe‐ cutes the binary from that temporary directory, and then deletes the binary after your program finishes.
- The **go build** command creates an executable called hello (or *hello.exe* on Windows) in the current directory. Run it and you unsurprisingly see Hello, world! printed on the screen.
    - Use go build to create a binary that is distributed for other people to use. Most of the time, this is what you want to do. Use the -o flag to give the binary a different name or location.
- The **go install** command takes an argument, which is the location of the source code repository of the project you want to install, followed by an @ and the version of the tool you want (if you just want to get the latest version, use @latest)
- The Go development tools include a command, **go fmt**, which automatically refor‐ mats your code to match the standard format. It does things like fixing up the white‐ space for indentation, lining up the fields in a struct, and making sure there is proper spacing around operators.
- There’s an enhanced version of go fmt available called **goimports** that also cleans up your import statements. It puts them in alphabetical order, removes unused imports, and attempts to guess any unspecified imports. Its guesses are sometimes inaccurate, so you should insert imports yourself.
    - **`goimports -l -w .`**
    - The -l flag tells goimports to print the files with incorrect formatting to the console. The -w flag tells goimports to modify the files in-place. The . specifies the files to be scanned: everything in the current directory and all of its subdirectories.
- There are tools that help to enforce this style. The first is called **golint**. (The term “linter” comes from the Unix team at Bell Labs; the first linter was written in 1978.) It tries to ensure your code follows style guidelines. Some of the changes it suggests include properly naming variables, formatting error messages, and placing comments on public methods and types.
    - **`golint ./...`**
- There is another class of errors that developers run into. The code is syntactically valid, but there are mistakes that are not what you meant to do. This includes things like passing the wrong number of parameters to formatting methods or assigning val‐ ues to variables that are never used. The go tool includes a command called **go vet** to detect these kinds of errors. Run go vet on your code with the command:
    - **`go vet ./...`**
- Rather than use separate tools, you can run multiple tools together with **golangci-lint**. It combines golint, go vet, and an ever-increasing set of other code quality tools.