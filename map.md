## Compile

Compiling in Mint writes a word or byte to the address pointed to by the heap pointer and then increments the heap pointer.

### Word compilation

```
Word (val -- )
```

```
:W \h@ ! 2\h\+ ;
```

### Byte compilation

```
Byte (val -- )
```

```
:B \h@ \! 1\h\+ ;
```

## Map

Mapping is a general operation that can be performed on an array with the purpose of transforming its values and returning another array.

```text
Map ( arr len fun -- arr' len' )
```

Map takes a pointer to an array and its length and a pointer to a function definition. 
The function definition takes one argument and returns a single value.

Map returns a new array with the same length as the source array.

```text
:M \f! \h@ \R\R ("@ \f@ \G \h@! 2\h\+ 2+) ' \h@ % -};

:M
\f!         \\ arr len                          \f:fun
\h@\R\R     \\ arr' arr len
(           \\ arr' arr
"@          \\ arr' arr arr[]
\f@ \G      \\ arr' arr val                     where val = f(arr[])
\h@! 2\h\+  \\ arr' arr                         compile val
2+          \\ arr' arr+2
)
'           \\ arr'
\h@ %       \\ arr' h arr'
-}          \\ arr' len
;

```

### Double

```
:D2*;
```

Double the numbers in an array.

```
> [1 4 3 6 2] ?D M
_[2 8 6 4]_
```

## Filtering

Filtering is a general operation that can be performed on an array with the purpose of returning a smaller array of filtered items.

```text
Filter ( arr len fun -- arr' len' )
```

Filter takes a pointer to an array and its length and a pointer to a function definition.
The function definition takes one argument and returns a single boolean value.

Filter returns a new array which is the same length of shorter than the source array.

```text
:F \f! \h@ \R\R ("@" \f@\G \(\h@! 2\h\+)(') 2+) ' \h@ % -};

:F
\f!                 \\ arr len                          \f:fun
\h@\R\R             \\ arr' arr len
(                   \\ arr' arr
"@"                 \\ arr' arr arr[] arr[]
\f@\G               \\ arr' arr arr[] bool              where bool = f(arr[])
\(\h@! 2\h\+)(')    \\ arr' arr                         if true compile arr[] else drop arr[]
2+                  \\ arr' arr+2
)
'                   \\ arr'
\h@ %               \\ arr' h arr'
-}                  \\ arr' len
;
