## Fold

Folding is a general operation that can be performed on an array with the purpose of reducing or "folding" it into a single value (often a number but not always).

```text
Fold ( val0 arr len fun -- val )
```

Fold takes an initial value `val0`, a pointer to an array and its length and a pointer to a function definition which takes two arguments and returns a single value.

It returns a single value.

```text
:F\f!($%@\f@\G$2+)';

:F
\f!         \\ val0 arr len                     \f:fun
(           \\ val arr
$%@         \\ arr val arr@
\f@ \G $    \\ val' arr                         where val' = f(val,arr@)
2+          \\ val' arr+2
)
'           \\ val
;

```

### Count

```
:C'1+;
```

Count the numbers in an array.

```
> 0 [1 4 3 6 2] ?C F
5
```

### Most

```
:M %%> \(\R')(');
```

Find the maximum number in an array.

```
> 0 [1 4 3 6 2] ?M F
6
```

### Least

```
:L %%< \(\R')(');
```

Find the minimum number in an array.

```
> 0 [1 4 3 6 2] ?L F
2
```

### Sum

```
:S+;
```

Sum of the numbers in an array.

```
> 0 [1 4 3 6 2] ?S F
16
```
