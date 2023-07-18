# Primitive Types

## The Zero Value

- Go, like most modern languages, assigns a default *zero value* to any variable that is
declared but not assigned a value. Having an explicit zero value makes code clearer
and removes a source of bugs found in C and C++ programs.

## Literals

- A *literal* in Go refers to writing out a number, character, or string. There are four common kinds of literals that you’ll find in Go programs. (There’s a rare fifth kind of
literal that we’ll cover when discussing complex numbers.)
- *Integer literals* are sequences of numbers; they are normally base ten, but different prefixes are used to indicate other bases: 0b for binary (base two), 0o for octal (base eight), or 0x for hexadecimal (base sixteen). You can use either or upper or lowercase letters for the prefix. A leading 0 with no letter after it is another way to represent an octal literal. Do not use it, as it is very confusing.
- To make it easier to read longer integer literals, Go allows you to put underscores in the middle of your literal. This allows you to, for example, group by thousands in base ten (1_234).
- *Floating point literals* have decimal points to indicate the fractional portion of the value. They can also have an exponent specified with the letter e and a positive or negative number (such as 6.03e23). You also have the option to write them in hexadecimal by using the 0x prefix and the letter p for indicating any exponent. Like integer literals, you can use underscores to format your floating point literals.
- *Rune literals* represent characters and are surrounded by single quotes. Unlike many other languages, in Go single quotes and double quotes are *not* interchangeable. Rune
literals can be written as single Unicode characters ('a'), 8-bit octal numbers ('\141'), 8-bit hexadecimal numbers ('\x61'), 16-bit hexadecimal numbers ('\u0061'), or 32-bit Unicode numbers ('\U00000061'). There are also several backslash escaped rune literals, with the most useful ones being newline ('\n'), tab ('\t'), single quote ('\''), double quote ('\"'), and backslash ('\\').
- There are two different ways to indicate *string literals*. Most of the time, you should use double quotes to create an *interpreted string literal* (e.g., type **"Greetings and
Salutations"**).
- If you need to include backslashes, double quotes, or newlines in your string, use a *raw string literal*. These are delimited with backquotes (`) and can contain any literal
character except a backquote. When using a raw string literal, we write our multiline greeting like so:

```
`Greetings and
"Salutations"`
```

<aside>
💡 As you will see in the section on variable assignment, there are situations in Go where
the type isn’t explicitly declared. In those cases, Go uses the *default type* for a literal

</aside>

## Variable Declaration

### var Versus :=

- Go uses the var keyword, an explicit type, and an assignment. It looks like this:

```go
var x int = 10
```

- If the type on the righthand side of the = is the expected type of your variable, you can leave off the type from the left side of the =. Since the default type of an integer literal is int, the following declares x to be a variable of type int:

```go
var x = 10
```

- Conversely, if you want to declare a variable and assign it the zero value, you can keep the type and drop the = on the righthand side:

```go
var x int
```

- You can declare multiple variables at once with var, and they can be of the same type:

```go
var x, y int = 10, 20
```

- all zero values of the same type or different types:

```go
var x, y int
var x, y = 10, "hello"
```

- There’s one more way to use var. If you are declaring multiple variables at once, you can wrap them in a *declaration list*:

```go
var (
	x int
	y = 20
	z int = 30
	d, e = 40, "hello" 
	f, g string
)
```

- Go also supports a short declaration format. When you are within a function, you can use the := operator to replace a var declaration that uses type inference:

```go
var x = 10 
x := 10
```

- The := operator can do one trick that you cannot do with var: it allows you to assign values to existing variables, too. As long as there is one new variable on the lefthand side of the :=, then any of the other variables can already exist:

```go
x := 10
x, y := 30, "hello"
```

- There is one limitation on :=. If you are declaring a variable at package level, you must use var because := is **not legal** outside of functions.

There are some situations within functions where you should avoid :=:

- When initializing a variable to its zero value, use var x int. This makes it clear that the zero value is intended.
- When assigning an untyped constant or a literal to a variable and the default type for the constant or literal isn’t the type you want for the variable, use the long var form with the type specified. While it is legal to use a type conversion to specify
the type of the value and use := to write x := byte(20), it is idiomatic to write var x byte = 20.
- Because := allows you to assign to both new and existing variables, it sometimes creates new variables when you think you are reusing existing ones (see “Shadowing Variables” on page 62 for details). In those situations, explicitly declare all of your new variables with var to make it clear which variables are new, and then use the assignment operator (=) to assign values to both new and old variables.

### Package-level Variables

- Avoid declaring variables outside of functions because they complicate data flow analysis.

## Booleans

- The zero value for a bool is false:

```go
**var** flag **bool** *// no value assigned, set to false*
**var** isAwesome = **true**
```

## Numeric Types

### Integer Types

- Go provides both signed and unsigned integers in a variety of sizes, from one to four bytes.

![Untitled](Primitive%20Types%20abdc859800ce430f8c09f0abd6094611/Untitled.png)

- The zero value for all of the integer types is 0.

### The special integer types

- A byte is an alias for uint8.
- The second special name is int. On a 32-bit CPU, int is a 32-bit signed integer like an int32. On most 64-bit CPUs, int is a 64-bit signed integer, just like an int64. Because int isn’t consistent from platform to platform, it is a compile-time error to assign, compare, or perform mathematical operations between an int and an int32 or int64 without a type conversion.
- The third special name is uint. It follows the same rules as int, only it is unsigned (the values are always 0 or positive).

### Integer Operators

- Go integers support the usual arithmetic operators: +, -, *, /, with % for modulus. The result of an integer division is an integer; if you want to get a floating point result, you need to use a type conversion to make your integers into floating point numbers.
- You can combine any of the arithmetic operators with = to modify a variable: +=, -=, *=, /=, and %=.
- You compare integers with ==, !=, >, >=, <, and <=.
- Go also has bit-manipulation operators for integers. You can bit shift left and right with << and >>, or do bit masks with & (logical AND), | (logical OR), ^ (logical XOR), and &^ (logical AND NOT). Just like the arithmetic operators, you can also combine all of the logical operators with = to modify a variable: &=, |=, ^=, &^=, <<=, and >>=.

### Floating point types

- There are two floating point types in Go, as shown in Table 2-2.

![Untitled](Primitive%20Types%20abdc859800ce430f8c09f0abd6094611/Untitled%201.png)

- Like the integer types, the zero value for the floating point types is 0.
- Floating point division has a couple of interesting properties. Dividing a nonzero floating point variable by 0 returns +Inf or -Inf.
- Dividing a floating point variable set to 0 by 0 returns NaN (Not a Number).
- While Go lets you use == and != to compare floats, don’t do it. Due to the inexact nature of floats, two floating point values might not be equal when you think they should be. Instead, define a maximum allowed variance and see if the difference between two floats is less than that. This value (sometimes called *epsilon*) depends on what your accuracy needs are; I can’t give you a simple rule. If you aren’t sure, consult your friendly local mathematician for advice.

### Strings and Runes

- This brings us to strings. Like most modern languages, Go includes strings as a built- in type. The zero value for a string is the empty string. Go supports Unicode; as we showed in the section on string literals, you can put any Unicode character into a string. Like integers and floats, strings are compared for equality using ==, difference with !=, or ordering with >, >=, <, or <=. They are concatenated by using the + operator.
- Strings in Go are immutable; you can reassign the value of a string variable, but you cannot change the value of the string that is assigned to it.
- Go also has a type that represents a single code point. The *rune* type is an alias for the int32 type, just like byte is an alias for uint8. As you could probably guess, a rune literal’s default type is a rune, and a string literal’s default type is a string.
- If you are referring to a character, use the rune type, not the int32 type. They might be the same to the compiler, but you want to use the type that clarifies the intent of your code.

## Explicit Type Conversion

- Go doesn’t allow automatic type promotion between variables. You must use a *type conversion* when variable types do not match. Even different-sized integers and floats must be converted to the same type to interact.

```go
var x int = 10
var y float64 = 30.2
var z float64 = float64(x) + y 
var d int = x + int(y)
```

- This strictness around types has other implications. Since all type conversions in Go are explicit, you cannot treat another Go type as a boolean. In many languages, a nonzero number or a nonempty string can be interpreted as a boolean true. Just like automatic type promotion, the rules for “truthy” values vary from language to language and can be confusing. Unsurprisingly, Go doesn’t allow truthiness. In fact, *no other type can be converted to a bool, implicitly or explicitly*. If you want to convert from another data type to boolean, you must use one of the comparison operators
(==, !=, >, <, <=, or >=). For example, to check if variable x is equal to 0, the code would be x == 0. If you want to check if string s is empty, use s == "".

## Using Const

- However, const in Go is very limited. Constants in Go are a way to give names to literals. They can only hold values that the compiler can figure out at compile time.
- Constants in Go are a way to give names to literals. There is *no* way in Go to declare that a variable is immutable.

```go
const x int64 = 10
const (
		idKey = "id"
    nameKey = "name"
)
```

- Whether or not to make a constant typed depends on why the constant was declared. If you are giving a name to a mathematical constant that could be used with multiple numeric types, then keep the constant untyped. In general, leaving a constant untyped gives you more flexibility. There are situations where you want a constant to enforce a type.
- Here’s what an untyped constant declaration looks like:

```go
**const** x = 10
```

All of the following assignments are legal:

```go
**var** y **int** = x
**var** z **float64** = x
**var** d **byte** = x
```

- Here’s what a typed constant declaration looks like:

```go
**const** typedX **int** = 10
```

This constant can only be assigned directly to an int. Assigning it to any other type produces a compile-time error.

## Unused Variables

- Another Go requirement is that *every declared local variable must be read*. It is a *compile-time error* to declare a local variable and to not read its value.
- The compiler’s unused variable check is not exhaustive. As long as a variable is read once, the compiler won’t complain, even if there are writes to the variable that are never read.
- While the **compiler** and **go vet** do not catch the unused assignments of 10 and 30 to x, **golangci-lint** detects them:

```bash
$ golangci-lint run
    unused.go:6:2: ineffectual assignment to `x` (ineffassign)
x := 10
        ^
    unused.go:9:2: ineffectual assignment to `x` (ineffassign)
x = 30 ^
```

<aside>
💡 The Go compiler won’t stop you from creating unread package- level variables. This is one more reason why you should avoid creating package-level variables.

</aside>

<aside>
💡 Perhaps surprisingly, the Go compiler allows you to create unread constants with const. This is because constants in Go are calculated at compile time and cannot have any side effects. This makes them easy to eliminate: if a constant isn’t used, it is simply not included in the compiled binary.

</aside>

## Variable Naming

- Idiomatic Go doesn’t use **snake case** (names like index_counter or number_tries). Instead, Go uses **camel case** (names like indexCounter or numberTries).
- In many languages, constants are always written in all **uppercase letters**, with words **separated by underscores** (names like INDEX_COUNTER or NUMBER_TRIES). Go does not follow this pattern.
- The smaller the scope for a variable, the shorter the name that’s used for it. It is very common in Go to see single-letter
variable names. For example, the names k and v (short for *key* and *value*) are used as the variable names in a for-range loop