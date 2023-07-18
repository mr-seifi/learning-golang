# Composite Types

# Arrays—To Rigid to Use Directly

- All of the elements in the array must be of the type that’s specified (this doesn’t mean they are always of the same type).

```go
var x [3]int
var x = [3]int{10, 20, 30}
var x = [12]int{1, 5: 4, 6, 10: 100, 15} // [1, 0, 0, 0, 0, 4, 6, 0, 0, 0, 100, 15]
var x = [...]int{10, 20, 30}
var x = [...]int{1, 2, 3}
var y = [3]int{1, 2, 3} 
fmt.Println(x == y) // prints true
var x [2][3]int

// ---
var x = [3]int{10, 20, 30}
x[0] = 10
fmt.Println(x[2])
fmt.Println(len(x))
```

- Arrays in Go are rarely used explicitly. This is because they come with an unusual limitation: Go considers the *size* of the array to be part of the *type* of the array. This makes an array that’s declared to be [3]int a different type from an array that’s declared to be [4]int. This also means that you cannot use a variable to specify the size of an array, because types must be resolved at compile time, not at runtime.
- What’s more, *you can’t use a type conversion to convert arrays of different sizes to identical types*.
- Due to these restrictions, don’t use arrays unless you know the exact length you need ahead of time.

# Slices

- The first thing to notice is that we don’t specify the size of the slice when we declare it:

```go
**var** x = []**int**{10, 20, 30}
```

<aside>
💡 Using [...] makes an array. Using [] makes a slice.

</aside>

- You can simulate multidimensional slices and make a slice of slices:

```go
var x [][]int
```

- You read and write slices using bracket syntax, and, just like with arrays, you can’t read or write past the end or use a negative index.
- The Zero Value for slices is `nil`. In Go, nil is an identifier that represents the lack of a value for some types.
- A slice isn’t **comparable**. It is a compile-time error to use == to see if two slices are identical or != to see if they are different. The only thing you can compare a slice with is nil:

```go
fmt.Println(x == nil) // prints true
```

<aside>
💡 The reflect package contains a function called DeepEqual that can compare almost anything, including slices.

</aside>

- We’ve already seen the built-in len function when looking at arrays. It works for slices, too, and when you pass a nil slice to len, it returns 0.

```go
var x = []int{1, 2, 3} 
x = append(x, 4)
x = append(x, 5, 6, 7)

y := []int{20, 30, 40} 
x = append(x, y...)
```

<aside>
💡 It is a compile-time error if you forget to assign the value returned from append.

</aside>

- Go is a *call by value* language. Every time you pass a parameter to a function, Go makes a copy of the value that’s passed in.

<aside>
💡 The Go runtime provides services like memory allocation and garbage collection, concurrency support, networking, and implementations of built-in types and functions.

</aside>

<aside>
💡 The Go runtime is compiled into every Go binary. This feature makes Go programs more portable and simplifies the deployment process, since there are no dependencies to worry about other than the binary itself.

</aside>

<aside>
💡 When a slice grows via append, it takes time for the Go runtime to allocate new memory and copy the existing data from the old memory to the new. The old memory also needs to be garbage collected. For this reason, the Go runtime usually increases a slice by more than one each time it runs out of capacity. The rules as of Go 1.14 are to double the size of the slice when the capacity is less than 1,024 and then grow by at least 25% afterward.

</aside>

- Just as the built-in len function returns the current length of a slice, the built-in cap function returns the current capacity of a slice. It is used far less frequently than len. Most of the time, cap is used to check if a slice is large enough to hold new data, or if a call to make is needed to create a new slice.
- While it’s nice that slices grow automatically, it’s far more efficient to size them once. If you know how many things you plan to put into a slice, create the slice with the correct initial capacity. We do that with the **make** function.

## make

- While useful, neither way allows you to create an empty slice that already has a length or capacity specified. That’s the job of the built-in make function. It allows us to specify the type, length, and, optionally, the capacity.

```go
x := make([]int, 5)
```

- This creates an int slice with a length of 5 and a capacity of 5. Since it has a length of 5, x[0] through x[4] are valid elements, and they are all initialized to 0.

<aside>
💡 One common beginner mistake is to try to populate those initial elements using append:

```go
x := make([]**int**, 5)
x = append(x, 10)
```

The 10 is placed at the end of the slice, *after* the zero values in positions 0–4 because append always increases the length of a slice. The value of x is now [0 0 0 0 0 10], with a length of 6 and a capacity of 10 (the capacity was doubled as soon as the sixth element was appended).

</aside>

- We can also specify an initial capacity with make:

```go
x := make([]**int**, 5, 10)
```

- This creates an int slice with a length of 5 and a capacity of 10. You can also create a slice with zero length, but a capacity that’s greater than zero:

```go
x := make([]**int**, 0, 10)
```

- In this case, we have a non-nil slice with a length of 0, but a capacity of 10. Since the length is 0, we can’t directly index into it, but we can append values to it:

```go
x := make([]**int**, 0, 10)
x = append(x, 5, 6, 7, 8)
```

The value of x is now [5 6 7 8], with a length of 4 and a capacity of 10.

<aside>
💡 Never specify a capacity that’s less than the length! It is a compile- time error to do so with a constant or numeric literal. If you use a variable to specify a capacity that’s smaller than the length, your program will panic at runtime.

</aside>

### Slicing Slices

```go
x := []int{1, 2, 3, 4} 
y := x[:2] // 1, 2
z := x[1:] // 2, 3, 4
d := x[1:3] // 2, 3
e := x[:] // 1, 2, 3, 4
fmt.Println("x:", x)
fmt.Println("y:", y)
fmt.Println("z:", z)
fmt.Println("d:", d)
fmt.Println("e:", e)
```

- When you take a slice from a slice, you are *not* making a copy of the data. Instead, you now have two variables that are sharing memory.

```go
x := []int{1, 2, 3, 4} 
y := x[:2]
z := x[1:]
fmt.Println(cap(x), cap(y), cap(z)) // 5, 5, 5
x[1] = 20
y[0] = 10
z[1] = 30
fmt.Println("x:", x) // 10, 20, 30, 4
fmt.Println("y:", y) // 10, 20
fmt.Println("z:", z) // 20, 30, 4

```

<aside>
💡 *append makes overlapping slices more confusing.* To avoid complicated slice situations, you should either never use append with a sub-slice or make sure that append doesn’t cause an overwrite by using a *full slice expression*.

</aside>

### Full Slice Expression

```go
y := x[:2:2]
z := x[2:4:4]
```

- The full slice expression includes a third part, which indicates the last position in the parent slice’s capacity that’s available for the sub-slice.

```go
x := make([]int, 0, 5)
x = append(x, 1, 2, 3, 4)
y := x[:2:2]
z := x[2:4:4]
fmt.Println(cap(x), cap(y), cap(z)) // 5, 2, 2
y = append(y, 30, 40, 50)
x = append(x, 60)
z = append(z, 70) 
fmt.Println("x:", x) // 1, 2, 3, 4, 60
fmt.Println("y:", y) // 1, 2, 30, 40, 50
fmt.Println("z:", z) // 3, 4, 70
```

## Converting Arrays to Slices

- Slices aren’t the only thing you can slice. If you have an array, you can take a slice from it using a slice expression. This is a useful way to bridge an array to a function that only takes slices. However, be aware that taking a slice from an array has the same memory-sharing properties as taking a slice from a slice.

```go
x := [4]int{5, 6, 7, 8} 
y := x[:2]
z := x[2:]
x[0] = 10 
fmt.Println("x:", x) // 10, 6, 7, 8
fmt.Println("y:", y) // 10, 6
fmt.Println("z:", z) // 7, 8
```

### Copy

- If you need to create a slice that’s independent of the original, use the built-in copy function.

```go
x := []int{1, 2, 3, 4} 
y := make([]int, 4) 
num := copy(y, x) 
fmt.Println(y, num) // [1, 2, 3, 4] 4
```

- The first is the destination slice and the second is the source slice.
- The *capacity* of x and y doesn’t matter; it’s the length that’s important.

```go
x := []int{1, 2, 3, 4} 
y := make([]int, 2) 
num = copy(y, x) // [1, 2] 2
```

```go
x := []int{1, 2, 3, 4} 
y := make([]int, 2) 
copy(y, x[2:]) // [3, 4], 2
```

```go
x := []int{1, 2, 3, 4} 
y := make([]int, 5) 
num = copy(y, x) // [1, 2, 3, 4, 0] 4
```

```go
x := []int{1, 2, 3, 4} 
y := make([]int, 5) 
num = copy(y[1:], x) // [0, 1, 2, 3, 4] 4
```

```go
x := []int{1, 2, 3, 4} 
num = copy(x[:3], x[1:]) 
fmt.Println(x, num) // [2, 3, 4, 4] 3
```

# Strings and Runes and Bytes

- You might think that a string in Go is made out of runes, but that’s not the case.
- Go uses a sequence of bytes to represent a string.
- Several Go library functions (and the for- range loop that we discuss in the next chapter) assume that a string is composed of a sequence of UTF-8-encoded code points.

<aside>
💡 According to the language specification, Go source code is always written in UTF-8. Unless you use hexadecimal escapes in a string literal, your string literals are written in UTF-8.

</aside>

- *Index expression*

```go
var s string = "Hello there" 
var b byte = s[6]
```

- *Slice expression*

```go
var s string = "Hello there" 
var s2 string = s[4:7]
var s3 string = s[:5]
var s4 string = s[6:]
```

- Go allows us to use slicing notation to make substrings and use index notation to extract individual entries from a string.
- Since strings are immutable, they don’t have the modification problems that slices of slices do.
- A string is composed of a sequence of bytes, while a code point in UTF-8 can be anywhere from one to four bytes long. Our previous example was entirely composed of code points that are one byte long in UTF-8, so everything worked out as expected.
- But when dealing with languages other than English or with emojis, you run into code points that are multiple bytes long in UTF-8.

![Untitled](Composite%20Types%2061299f9172614bbf8fb3c6e58ca01273/Untitled.png)

- In this example, s3 will still be equal to “Hello.” The variable s4 is set to the sun emoji. But s2 is not set to “o <sun>” Instead, you get “o <unknown>” That’s because we only copied the first byte of the sun emoji’s code point, which is invalid.
- If you try to print the length of the s with built-in len… it outputs 10 instead of 7 as it represents sun by 4 bytes (UTF-8).
- Go allows you to use slicing and indexing syntax with strings, you should only use it when you know that your string only contains characters that take up one byte.
- Because of this complicated relationship between runes, strings, and bytes, Go has some interesting type conversions between these types. A single rune or byte can be converted to a string.

```go
var a rune = 'x'
var s string = string(a) 
var b byte = 'y'
var s2 string = string(b)
```

<aside>
💡 A common bug for new Go developers is to try to make an int into a string by using a type conversion:

```go
**var** x **int** = 65
**var** y = string(x)
fmt.Println(y)
```

This results in y having the value “A,” not “65.” As of Go 1.15, go vet blocks a type conversion to string from any integer type other than rune or byte.

</aside>

- As you can convert byte and rune to string and vice versa, lets convert string to slices.

 

![Untitled](Composite%20Types%2061299f9172614bbf8fb3c6e58ca01273/Untitled%201.png)

```go
fmt.Println(bs) // [72 101 108 108 111 44 32 240 159 140 158]
fmt.Println(rs) // [72 101 108 108 111 44 32 127774]
```

- The first output line has the string converted to UTF-8 bytes. The second has the string converted to runes.

# Maps

- Go provides a built-in data type for situations where you want to associate one value to another.
- The map type is written as `map[keyType]valueType`.
- The zero value for a map is nil.

```go
var nilMap map[string]int // **In this case, nilMap is declared to be a map with string keys and int values.**
```

- A nil map has a length of 0. Attempting to read a nil map always returns the zero value for the map’s value type. However, attempting to write to a nil map variable causes a panic.
- We can use a := declaration to create a map variable by assigning it a *map literal*:

```go
totalWins := map[string]int{}
```

- In this case, we are using an empty map literal. This is not the same as a nil map. It has a length of 0, but you can read and write to a map assigned an empty map literal.
- Here’s what a nonempty map literal looks like:

```go
teams := map[string][]string {
	"Orcas": []string{"Fred", "Ralph", "Bijou"}, 
	"Lions": []string{"Sarah", "Peter", "Billie"}, 
	"Kittens": []string{"Waldo", "Raul", "Ze"},
}
```

[Map Implementation of Golang](https://oreil.ly/kIeJM)

```go
totalWins := map[string]int{} 
totalWins["Orcas"] = 1 
totalWins["Lions"] = 2 
fmt.Println(totalWins["Orcas"]) 
fmt.Println(totalWins["Kittens"]) 
totalWins["Kittens"]++ 
fmt.Println(totalWins["Kittens"]) 
totalWins["Lions"] = 3 
fmt.Println(totalWins["Lions"])
```

- You cannot use := to assign a value to a map key.
- When we try to read the value assigned to a map key that was never set, the map returns the zero value for the map’s value type.
- If you wanna find out if a key in you use comma ok idiom.

```go
m := map[string]int{ 
"hello": 5,
"world": 0, 
}

v, ok := m["hello"] // 5 true
fmt.Println(v, ok)
v, ok = m["world"] // 0 true
fmt.Println(v, ok)
v, ok = m["goodbye"] // 0 false
fmt.Println(v, ok)
```

- For deleting key-values from your map:

```go
m := map[string]int{ 
"hello": 5,
"world": 10,
}

delete(m, "hello")
```

- Go doesn’t have set data structure. Therefore you can simulate a map as a set:

```go
intSet := map[int]bool{} 
vals := []int{5, 10, 2, 5, 8, 7, 3, 9, 1, 2, 10} 
for _, v := rangevals{
	intSet[v] = true 
}
fmt.Println(len(vals), len(intSet)) 
fmt.Println(intSet[5]) 
fmt.Println(intSet[500])
if intSet[100] {
    fmt.Println("100 is in the set")
}
```

- Using **`map**[**int**]**struct**{}{}` has some pros and cons which I’m gonna mention it.
    - The advantage is that an empty struct uses zero bytes, while a boolean uses one byte.
    - The disadvantage is that using a struct{} makes your code more clumsy.

# Structs

- Maps are a convenient way to store some kinds of data, but they have limitations. They don’t define an API since there’s no way to constrain a map to only allow certain keys. Also, all of the values in a map must be of the same type.
- When you have related data that you want to group together, you should define a *struct*.

<aside>
💡 Go doesn’t have classes, because it doesn’t have inheritance. This doesn’t mean that Go doesn’t have some of the features of object-oriented languages, it just does things a little differently.

</aside>

```go
type person struct { 
	name string
	age int
	pet string 
}
```

- Once a struct type is declared:

```go
var fred person
bob := person{}
```

- A zero value struct has every field set to the field’s zero value.
- Unlike maps, there is no difference between assigning an empty struct literal and not assigning a value at all.

```go
julia := person{
  "Julia",
	40,
	"cat", 
}
```

```go
beth := person{
  age:  30,
  name: "Beth",
}
```

- You cannot mix the two struct literal styles.
- Access a field using dot notation:

```go
bob.name = "Bob"
fmt.Println(beth.name)
```

## Anonymous Structs

- Instead of using `type` keyword, use `var` keyword:

```go
var person struct {
	name string
	age int
	pet string 
}

person.name = "bob"
person.age = 50
person.pet = "dog"

pet := struct { 
	name string 
	kind string
}{
  name: "Fido",
  kind: "dog",
}
```

- Anonymous structs are useful while this two situations are met:
    - when you translate external data into a struct or a struct into external data (like JSON or protocol buffers). This is called ***unmarshaling*** and ***marshaling***
    data.
    - Writing tests is another place where anonymous structs pop up.

## Comparing & Converting Structs

- Go doesn’t allow comparisons between variables of different primitive types, Go doesn’t allow comparisons between variables that represent structs of different types.
- Go does allow you to perform a type conversion from one struct type to another *if the fields of both structs have the same names, order, and types*.
- Suppose (All of them compared with firstPerson):

```go
type firstPerson struct { 
	name string
	age int 
}

type secondPerson struct { 
	name string
	age int 
} // Possible convert but not compare (because they're not the same type)

type thirdPerson struct { 
	age int
	name string
} // Not possible neither convert nor compare

type fourthPerson struct { 
	firstName string
	age int 
} // Not possible neither convert nor compare

type fifthPerson struct { 
	name string
	age int 
	favoriteColor string
} // Not possible neither convert nor compare
```

- Anonymous structs add a small twist to this: if two struct variables are being com‐ pared and at least one of them has a type that’s an anonymous struct, you can com‐ pare them without a type conversion if the fields of both structs have the same names, order, and types.

```go
type firstPerson struct { 
	name string
	age int 
}
f := firstPerson{
  name: "Bob",
	age: 50, 
}
var g struct { 
	name string
	age int 
}
// compiles -- can use = and == between identical named and anonymous structs
g = f 
fmt.Println(f == g)
```