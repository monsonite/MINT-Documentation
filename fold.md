## Fold

:F$\R\f!($%@\f@\G$2+);

````

```text

:F          \\ val0 fun arr len -- val
\R          \\ val0 arr len fun
\f!         \\ val0 arr len                     \f:fun
(           \\ val arr
$%@         \\ arr val arr@
\f@ \G $    \\ val' arr                         where val' = f(val,arr@)
2+          \\ val' arr+2
)
;

````

Alternative implementation (slower + uses more variables)

```

:F$\a!$\o!(\a@\i@{+@\o@\G);

```

```text

:F          \\ val0 fun arr len -- val
$\a!        \\ val0 fun len                    \a:arr
$\o!        \\ val0 len \a:arr \o:fun
(           \\ val
\a@ \i@     \\ val a
{+ @        \\ val a[i]
\o@ \G      \\ val'                             where val' = o(val,a[i])
)
;

```

```

### Count

```

:C'1+;

```

Count the numbers in an array.

```

> 0 ?C [1 4 3 6 2] F
> 5

```

### Most

```

:M %%> \(\R')(');

```

Find the maximum number in an array.

```

> 0 ?M [1 4 3 6 2] F
> 6

```

### Least

```

:L %%< \(\R')(');

```

Find the minimum number in an array.

```

> 0 ?L [1 4 3 6 2] F
> 2

```

### Sum

```

:S+;

```

Sum of the numbers in an array.

```

> 0 ?S [1 4 3 6 2] F
> 16

```

```
