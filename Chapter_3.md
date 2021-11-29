# Chapter 3

## Number Entry, Basic Arithmetic and Printing

Fundamental to all computer languages is the means to input data to the system and to return printed output. MINT is no exception and has several commands to alllow efficient communication with a serial terminal program, usually running on a laptop or desktop PC.

To keep things simple, MINT only has a few methods of data entry and printing and these will be introduced in this chapter.

As MINT uses a 16-bit integer system for all calculations, numbers are restricted to the range of `0` to `65535`.

Numbers are stored directly onto the stack, which as we learnt in [Chapter 2](./Chapter_2.md) is a form of push-down memory storage method sometime known as Last In, First Out or _LIFO_.

Numbers are PUSHED onto the stack when we want to store them and POPPED off the stack when we want to retrieve them for use.

Decimal numbers will be more familliar to most users, so we will start with these.

To _PUSH_ a number onto the stack we just type it in and press _ENTER_

```
12345 <ENTER>
```

The MINT program will return with the `>` prompt to show that the number has been accepted. We can then type one or more numbers which will also be placed onto the stack. You may place about 50 numbers onto the stack, but it is unusual to need to use more than about 4 at a time. The GOLDEN RULE is that consecutive numbers must be separated by one or more space characters. The MINT interpreter takes each number in turn, until it "sees" the space, and places it onto the stack. It then works its way along the line handling each number until it encounters the end of the line, signified by _ENTER_.

```
35 123 999 1066 <ENTER>
```

We now have 5 decimal numbers placed onto the stack which can be used for later purposes. If we want to check what is currently on the stack we have a special command `Control P` which prints out the current contents of the stack to the screen.

This is very useful when programming, to be able to examine what is on the stack. It prints out the contents of the stack, but leaves the numbers in place.

On typing `Control P`, MINT will respond with something similar to this:

```
> => 12345 00035 00123 00999 01066
```

The `=>` indicates that this is the current contents of the stack. We can see that they match the numbers that we entered, starting with `12345` on the left and `01066` on the right. Notice that decimal numbers are printed out always as 5 digits, with leading zeros inserted where appropriate.

Printing out the top of the stack.

Often when programming, we choose to place the result of a computation onto the top of the stack, and all we want to do is print it out so that we can see what the result is. For this we use the DOT command `.`, which is a single fullstop or period character.

This will remove the top number off the stack and print it out to the terminal as a 5 digit decimal integer. Note that it actively removes it from the top of the stack (or consumes it) so the next number will automatically rise to the top of the stack.

If we now type a single `.`, followed by _ENTER_, MINT will reply with

```
01066

>
```

And if we use `Control P`. to examine the stack we are left with

```
> => 12345 00035 00123 00999

>
```

If we now type 4 `.`s followed by _ENTER_, MINT will print out the last 4 remaining numbers on the stack, leaving it empty. However, instead we will introduce one of the arithmetic operations ADD, for which we use the `+` character followed by _ENTER_
This will ADD the top two members of the stack, in our case 00999 and 00123

Type + followed by _ENTER_ and then `Control P` followed by _ENTER_ to examine the new status of the stack

```
> -

> => 12345 00035 01122
```

What has happened? The top two members of the stack have been added together, effectively consuming them and their SUM which is `001122` has been put in their place.

Now let's try a subtraction. This will subtract the top number on the stack from the second member and replace them with the difference.

Type - for subtraction, followed by _ENTER_. Then use `Control P` _ENTER_ to re-examine the stack.

```
> -

> => 12345 64449
```

Ooops, that doesn't look right. We were expecting 35 - 1122, which should give -1087. Well if we remember the lesson from [Chapter 2](./Chapter_2.md), we will recall that negative numbers use a system called signed integers, and that `64489` is actually how MINT represents the negative number `-1087`.

We can prove this by _negating_ the top member of the stack. For this we have the command UNDERSCORE or `_` which is used to convert the negative number representation into a positive answer.

If you now type `_` _ENTER_ followed by `Control P`, you will see that the top member of the stack has been converted to `01087`, which is the _MAGNITUDE_ of the negative number

```
> _

> => 12345 01087
```

OK, now we have just two numbers left on the stack. Let's try out _DIVISION_ which is signified by the `/` character. If you now type / _ENTER_ followed by `Control P`, you will see the following stack contents

```
> /

> => 00388 00011
```

What has happened here? Well we have divided the second member of the stack `12345` by the first member of the stack.

`12345` divided by `1087` equals `11` with `388` as the remainder, both which have been left on the stack.

Just for fun, we can perform a _MULTIPLICATION_ using the `*` character followed by _ENTER_. Then again we can examine the stack

```
> -

> => 00000 04268
```

The top of the stack has been replaced by `04268` which is what we get when we multiply `11` by `388`.

## Hexadecimal Number Entry

Often in computing we wish to express numbers in a base other than _DECIMAL_. The most commonly used bases in computing are _HEXADECIMAL_ (BASE 16) and _BINARY_ (BASE 2).

In MINT we offer _HEXADECIMAL_ as well as _DECIMAL_, and have some special commands to make using _HEXADECIMAL_ easier.

If you are unsure about the _HEXADECIMAL_ number system, we recommend looking at an online primer, but needless to say, anything that you can do in DECIMAL, like arithmetic and logic operations can equally be done in _HEXADECIMAL_, and sometimes working in "HEX" is easier.

MINT allows HEX numbers up to 4 digits long, and each must be preceded by the `#` character and separated from the next by a space, for example type the following onto the stack followed by _ENTER_. Don't forget the final space after `#C9` !!

```
#55 #FFFF #1066 #7FF #C9
```

Now examine the stack in the usual way by using Control P.

```
> => 00085 65535 04198 02047 00201

>
```

Although the numbers were entered as HEX, MINT has automatically displayed them as DECIMAL. If you want to see them displayed in HEX notation, we have a special command Control B. This toggles the BASE from `10` to `16` so when you examine the stack they will be displayed in HEX notation.

Type `Control B` followed by `Control P`

```
> => 0055 FFFF 1066 07FF 00C9
```

The numbers are being displayed in HEX.

We can also print out the values on the stack in HEX. For this we use the `,` command. It works similarly to the DOT command but expresses the top member on the stack as a 4 digit HEX number.

Type five COMMAS followed by _ENTER_

```
> , , , , ,
> 00C9 07FF 1066 FFFF 0055
```

The numbers have been returned to us starting with `00C9` which was on the top of the stack and ending with `0055` which was the first number we placed on the stack. Remember that the stack uses a Last In, First Out system to store numbers.

We can ADD, SUBTRACT, MULTIPLY and DIVIDE HEX numbers. Try out the following code snippets of MINT to prove what can be done

```
#7FF #1 + ,

#100 #10 - ,

#10 #10 * ,
```

We can even mix HEX and DECIMAL number input, as in this DIVISION example

```
> 8192 #10 / , ,
> 0200 0000
```

`8192` is DECIMAL, `#10` is the equivalent of `16` in DECIMAL. `8192` divided by `16` is `512`. Which appears as `0200` when we print it out in HEX.

## SUMMARY

In this chapter we have learnt how to enter numbers onto the stack both in DECIMAL and HEXADECIMAL notation.

Remember that numbers MUST be separated by a SPACE character, and HEX numbers must also be preceded by the HASH # character.

We have also introduced the four basic arithmetic operators `+` `-` `*` `/` and the NEGATE operator `_` which will return the MAGNITUDE of a NEGATIVE number.

The `.` command is used to (destructively) print out the top member of the stack in DECIMAL

The `,` command is used to (destructively) print out the top member of the stack in HEX

We have learnt that `Control P` allows us to examine the current contents of the stack without modifying it.

`Control B` is used to toggle between DECIMAL and HEXADECIMAL bases.

In this chapter we covered the following commands:

| Symbol | Description                                               | Effect   |
| ------ | --------------------------------------------------------- | -------- |
| +      | 16-bit integer addition ADD                               | a b -- c |
| -      | 16-bit integer subtraction SUB                            | a b -- c |
| \*     | 8-bit by 8-bit integer multiplication MUL                 | a b -- c |
| /      | 16-bit by 8-bit division DIV                              | a b -- c |
| \_     | 16-bit negation (2's complement) NEG                      | a -- b   |
| #      | the following number is in hexadecimal HASH               | a --     |
| .      | print the top member of the stack as a decimal number DOT | a --     |
| ,      | print the number on the stack as a hexadecimal COMMA      | a --     |

Control Commands:

| Key | Description                     |
| --- | ------------------------------- |
| ^B  | toggle base DECIMAL/HEXADECIMAL |
| ^P  | print stack                     |
