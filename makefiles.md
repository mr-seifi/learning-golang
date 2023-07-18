# Makefiles

- Each possible operation is called a *target*. The .**DEFAULT_GOAL** defines which target is run when no target is specified. In our case, we are going to run the **build** target. Next we have the target definitions. The word before the colon (:) is the name of the target. Any words after the target (like vet in the line build: vet) are the other targets that must be run before the specified target runs. The tasks that are per‐ formed by the target are on the indented lines after the target. (The .**PHONY** line keeps make from getting confused if you ever create a directory in your project with the same name as a target.)
- Here is an example of a Makefile:

```makefile
.DEFAULT_GOAL := build
fmt:
		go fmt ./...
.PHONY:fmt
lint: fmt
    golint ./...
.PHONY:lint
vet: fmt
    go vet ./...
.PHONY:vet
build: vet
    go build hello.go
.PHONY:build
```

```bash
make
```

By entering a single command, we make sure the code was formatted correctly, vet the code for nonobvious errors, and compile. We can also run the linter with make lint, vet the code with make vet, or just run the formatter with make fmt. This might not seem like a big improvement, but ensuring that formatting and vetting always happen before a developer (or a script running on a continuous integration build server) triggers a build means you won’t miss any steps.