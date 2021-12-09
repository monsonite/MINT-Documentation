## Hashing

### Stack

Hashing the values on the stack

```
:K 0$ ($1+^);               ; x1...xn num -- hash 
```

### Array

```
:H 0\R\R ( $%@ 1+^ $ 2+)';       ; arr len -- hash
```
