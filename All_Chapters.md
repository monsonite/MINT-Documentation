# Chapter 1

## Introduction to MINT

This chapter is only intended as a _Quick Start Guide_. The topics raised in this chapter will be covered in detail in the subsequent chapters.

### What is MINT ?

MINT is a tiny, text based, interpreted language inspired by Forth.

On the Z80 it can be implemented in fewer than 1700 bytes of machine code and it is relatively quick compared to other interpreted languages.

MINT is a form of shorthand. It uses plain text and printable characters, so it can be written and edited using any text editor, using the text editor facilities for file storage.

Moreover, because the MINT source is just plain text, phrases or even complete programs written in MINT can be cut from the text editor and pasted into the MINT interpreter using a serial terminal application, such as Putty, Tera Term or similar.

As MINT is an interpreted language, there is no compilation required and the source code is executed directly.

Here is an example of MINT source code, which prints out the Fibonacci Sequence.

```
:F(a@b@+c!b@a!c@b!c@.);
0a! 1b! a@. b@. 23F
```

Once this text is pasted to the target hardware, on pressing the <ENTER> key it will be executed immediately, printing out the familiar Fibonacci Sequence like so:

```
00000 00001 00001 00002 00003 00005 00008 00013 00021 00034 00055 00089 00144 00233 00377 00610 00987 01597 02584 04181 06765 10946 17711 28657 46368

>
```

MINT is a compact language, like shorthand, but for clarity it is possible to include extra spaces to make the source more readable. The first line of the Fibonacci definition could be rewritten as:

```
:F (a@ b@ + c! b@ a! c@ b! c@ . ) ;
```

As you can see, MINT uses a variety of familiar characters such as `+ - *` and `/` to implement arithmetical and logical operations, plus alpha-numerical characters, punctuation marks and other symbols. In total, MINT has about 30 basic instructions consisting of single, printable ascii characters.

For the purpose of this document, these characters will be shown in a highlighted typeface to distinguish them from other text.

The characters will be introduced over the next 8 chapters a few at a time.

If you have come from a background of assembly language, MINT is pitched at a higher level, and allows complex programming tasks to be done without the use of an assembler.

The usual environment is just a serial terminal program running on a laptop or desktop PC. A serial interface cable or adaptor will connect to the target single board computer or _SBC_.

MINT can be implemented on any microprocessor or microcontroller and once installed, source code can run on any target without modification.

This is achieved by implementing a 16-bit virtual machine, using assembly language on the target processor. Once this interpreter core is created, MINT source code will equally run on a Z80 as it will on a 6502 or any other cpu. The interpreter core is typically between 1K bytes and 2K bytes.

MINT is closely related to Forth, but much smaller in size and less complex. Like Forth it uses a parameter stack which is used to store data in memory using a push-down mechanism. The stack will be further described in Chapter 2 and Chapter 3.

### Printing Numbers

MINT uses reverse Polish notation (RPN) for it's arithmetic operations. This is a method that was popular on HP electronic calculators. The main feature of RPN is you have to put the operands before the operator.

For example, if you want to add two numbers you just type the following at the terminal keyboard:

`123 456 + .`

The numbers `123` then `456` are placed onto the STACK. The `+` command ADDs them together and leaves their sum as a result on the top of the stack. The DOT `.` is a shorthand character to instruct the MINT interpreter to print the result on the top of the stack to the terminal screen.

When you hit `<ENTER>` the result will be displayed thus

```
00579
>
```

The `>` is the cursor-prompt that confirms that the code has been executed and control has been passed back to the User.

MINT defaults to using DECIMAL arithmetic, but HEXADECIMAL (BASE 16) is also available. More about this in Chapter 3.

Decimal numbers between 0 and 65535 are accepted, and printout is always in the form of a 5 digit number with leading zeros inserted where appropriate.

### Printing Text

Most computer languages are demonstrated by printing the text string `Hello World` on the terminal display. MINT is no exception, and it is very simple to print text to the screen. Anything enclosed between back ticks `text` will be printed
`\N` is the way in which we send a _NEWLINE_ character to the terminal.

```
`Hello World` \N   <ENTER>
Hello World
>
```

# Chapter 2

## Fundamentals

In Chapter 1 we introduced the absolute basics of the MINT interpreter with a couple of very simple examples.

This chapter will further explore the MINT interpreter introducing the basic concepts and how the MINT interpreter works.

Chapters 3 to 9 will introduce the basic language a few commands at a time. Each chapter includes code examples that illustrate the use of the language.

Chapter 10 will introduce the extended language commands.

At the end of this document is a Glossary of all the commands used in MINT. Each chapter has an excerpt from the Glossary to summarise the commands that have been discussed in that chapter.

Like other small interpreted languages, the intention of MINT is to create a 16-bit virtual machine by combining the mostly 8-bit operations available on the Z80, to provide 16-bit integer arithmetic and variable handling.

The language needs the basic arithmetic operations of ADD, SUBTRACT, MULTIPLY and DIVIDE. These are implemented as 16-bit integer operations and invoked using the familiar characters `+`, `-`, `*` and `/`.

These are augmented by the bitwise Boolean operators AND, OR, XOR, INVERT and 2's complement NEGATE.

With MINT, these instructions are just one byte long and a look-up table is used instead of a switch-case structure. When using an 8-bit microprocessor, such as the Z80, it is simpler and faster to handle 8-bit instructions, so MINT uses a bytecode system, rather than the 16-bit threaded code that is used by a conventional Forth.

In the Chapter 1 example above

```
123 456 + .	<ENTER>
```

The numerical strings `123` and `456` are evaluated as 16-bit binary numbers and placed on the data stack. The plus symbol `+` is interpreted as a jump to the routine that performs a 16-bit addition of the top two elements on the data stack, placing their sum on the top of the data stack. The dot character prints out the top value of the data stack, consuming it at the same time.

In addition to the arithmetic and boolean operations, there are also the three comparison operators:

Greater Than `>`,

Less Than `<`

Equal `=`

The top two elements on the stack will be compared, resulting in 1 if the comparison is TRUE and 0 if the comparison is FALSE.

With the comparison operators, it becomes possible to develop conditionally executed code, introduced in Chapter 6, which forms the basis of program control words, such as IF, THEN, ELSE, and looping and branching structures.

In total there are approximately 30 characters that are recognised as the internal instruction set, or primitives. From these characters the user can construct further definitions to extend the usefulness of the language.

### How MINT Works.

MINT is an interpreted language that uses printable ascii characters as its "instructions". There are 95 such characters:

26 Uppercase letters - used as User Commands `A` to `Z`
26 Lowercase letters - used as User Variables `a` to `z`
10 Numerals - used for number entry `0` to `9`
33 arithmetic and punctuation symbols - used to command the program operation

The interpreter scans a text string, held in a text buffer, one character at a time. It then uses a look-up table to broadly categorise the current character into one of the above groups.

For each category of character there is a handling routine, which determines how the character should be processed.

### NUMBERS

A number string such as 1234 will be scanned one digit at a time and converted into a 16-bit binary number using a routine called num\_ . The converted binary number is then placed on the top of the data stack which is used as a means of temporary storage, before being used later. Multiple numbers may be entered in a sequence separated by spaces: `1234 5678 3579` When the return key is pressed they will be processed in turn and each placed onto the stack. They may then be used as operands or parameters for a calculation or other function.

### VARIABLES

User Variables are assigned to the lowercase alpha characters using a routine called var\_ The user variables are stored in an array of 26 pairs of bytes in RAM. The lowercase character is a shorthand way of addressing the pair of bytes that holds the variable. It is not usually necessary to know specifically at what address the variable is held at, as it can always be accessed using its name.

For example, the variable addressed by the lowercase character `a` is held in RAM locations 34816 and 34817. Variable `b` will be held in the next locations 34818 and 34819 and so on up to `z`.

When a lowercase character is interpreted the variable handler routine converts it to a 16-bit address, and places that address on the top of the stack.

### COMMANDS

User Commands are what gives MINT its power and flexibility. Each uppercase letter is a substitute for an address in RAM, where the users code routines are held. For example you may have a routine which produces a hexadecimal dump of the contents of memory. You choose to use the D command to initiate this routine, D for DUMP. You may also pass parameters to a user routine via the stack. In the case of a hex dump routine it would be common to give it the starting address of the section you want to dump, and this might be written 1234 D. On pressing return, the command will be interpreted and the dump routine will commence printing from location 1234. There are 26 User Commands plus some Alternate User Commands which is usually enough for most small applications.

### PRIMITIVES

A primitive is a built in function, normally stored in ROM and not usually needed to be modified by the User. Primitives will include the familiar mathematical functions such as ADD, SUBtract, MULtiply and DIVide, and also boolean logic operations such as AND, OR, XOR and INVert.

There are also a small group of primitives that perform operations on the stack, DUP is used to duplicate the top item, DROP will remove the top item, making the second item available. SWAP will exchange the top two items, effectively placing the second item on top.

In total, MINT contains 33 primitives which are executed when the interpreter finds the relevant symbol. Some of these will be commonly used arithmetic symbols like `+` and `-` Others are allocated to punctuation symbols. The full-stop, or dot character `.` is used to print out the number held on the top of the stack.

### Using MINT on the RC2014

MINT was developed for the RC2014 Micro Z80 Single Board Computer. This board is supplied with a comprehensive Monitor program (The Small Computer Monitor (SCM) by Stephen Cousins). A 32K ROM contains the monitor and BASIC between $0000 and $7FFF. The 32K RAM starts at $8000, and MINT is loaded in to run from address $8100.

If necessary, you can use the serial getchar and putchar routines that are available within the Small Computer Monitor

See the User Manual pages 45 and 46 on how this is done

https://smallcomputercentral.files.wordpress.com/2018/05/scmon-v1-0-userguide-e1-0-0.pdf

MINT was assembled using asm80.com, an online 8-bit assembler. It will generate an Intel Hex file that can be pasted into RAM at address $8000 using a serial terminal program. I use TeraTerm when working within the windows environment.

Once the MINT code image is pasted into RAM you can run it using the Go command "G8000"

On initialisation it will present a user prompt `>` followed by a CR and LF. It is now ready to accept commands from the keyboard. MINT currently uses decimal numbers for calculations - a maximum integer of 65535.

MINT has about 30 built in commands called primitives. They are mostly allocated to arithmetical and punctuation symbols.

There are 26 User Defined Commands that use uppercase alpha characters `A`-`Z`

There are 26 User Variables that are assigned to lowercase alpha characters `a`-`z`

MINT turns the Z80 into a 16-bit Virtual Machine with 30 instructions, 26 Macros and 26 Registers (variables). This relieves you from the tedium of Z80 assembly language, and presents the user with a very compact, human readable, interactive, extendable bytecode language.

### Later in this manual

Chapter 3 Number Entry, Basic Arithmetic and Printing

Chapter 4 Stack Operations

Chapter 5 Introducing User Definitions

Chapter 6 Conditional Code Execution and Loops

Chapter 7 Variables

Chapter 8 Arrays

Chapter 9 Boolean and Logical Operations

Chapter 10 Extended and Alternate Commands

Glossary - A list of all commands used in MINT

# Chapter 3

## Number Entry, Basic Arithmetic and Printing

Fundamental to all computer languages is the means to input data to the system and to return printed output. MINT is no exception and has several commands to allow efficient communication with a serial terminal program, usually running on a laptop or desktop PC.

To keep things simple, MINT only has a few methods of data entry and printing and these will be introduced in this chapter.

As MINT uses a 16-bit integer system for all calculations, numbers are restricted to the range of `0` to `65535`.

Numbers are stored directly onto the stack, which as we learnt in [Chapter 2](./Chapter_2.md) is a form of push-down memory storage method sometime known as Last In, First Out or _LIFO_.

Numbers are PUSHED onto the stack when we want to store them and POPPED off the stack when we want to retrieve them for use.

Decimal numbers will be more familiar to most users, so we will start with these.

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

Often when programming, we choose to place the result of a computation onto the top of the stack, and all we want to do is print it out so that we can see what the result is. For this we use the DOT command `.`, which is a single full-stop or period character.

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

Oops, that doesn't look right. We were expecting 35 - 1122, which should give -1087. Well if we remember the lesson from [Chapter 2](./Chapter_2.md), we will recall that negative numbers use a system called signed integers, and that `64489` is actually how MINT represents the negative number `-1087`.

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

Just for fun, we can perform a _MULTIPLICATION_ using the `*` character followed by _ENTER_. Then again we can examine the stack using `Control P`

```
> *  <ENTER>

> => 00000 04268
```

The top of the stack has been replaced by `04268` which is what we get when we multiply `11` by `388`.

## Hexadecimal Number Entry

Often in computing we wish to express numbers in a base other than _DECIMAL_. The most commonly used bases in computing are _HEXADECIMAL_ (BASE 16) and _BINARY_ (BASE 2).

In MINT we offer _HEXADECIMAL_ as well as _DECIMAL_, and have some special commands to make using _HEXADECIMAL_ easier.

If you are unsure about the _HEXADECIMAL_ number system, we recommend looking at an online primer, but needless to say, anything that you can do in DECIMAL, like arithmetic and logic operations can equally be done in _HEXADECIMAL_, and sometimes working in "HEX" is easier.

MINT allows HEX numbers up to 4 digits long, and each must be preceded by the `#` character and separated from the next by a space, for example type the following onto the stack followed by _ENTER_. Don't forget the final space after `#C9` !!

```
#55 #FFFF #1066 #7FF #C9  <ENTER>
```

Now examine the stack in the usual way by using `Control P`.

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

We can also print out the values on the stack in HEX. For this we use the COMMA `,` command. It works similarly to the DOT command but expresses the top member on the stack as a 4 digit HEX number.

Type five COMMAS followed by _ENTER_

```
> , , , , ,   <ENTER>
00C9 07FF 1066 FFFF 0055
>
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

# Chapter 4

## Stack Operations

In Chapter 3 we looked at entering numbers (PARAMETERS) onto the stack in both DECIMAL and HEX formats and using them to perform basic arithmetic operations. Using DOT and COMMA we showed how results could be printed out to the screen. In reality, we were using MINT to perform as a basic 4 function CALCULATOR.

Using a stack is a powerful concept, it has its origins in the 1920s and further popularised in the 1950s when electronic computers started to become available. It provides a convenient means to store temporary data prior to performing some operation.

The stack has a few operations that help us to keep it in order, and in this chapter we will be looking at the stack manipulation commands. MINT uses 4 basic stack manipulation commands and they are essential for efficient use of the language.

Think of them as housekeeping for the stack - helping to keep everything tidy and in the correct order prior to the calculation or other operation.

The four basic commands are _DUP_, _DROP_, _SWAP_ and _OVER_:

_DUP_ Duplicate the top member of the stack, in mint we use the command `"` which suggests we have two identical items on the stack

_DROP_ Throw away the top member of the stack exposing the 2nd member. We use the single quote character `'` to represent DROPPED

_SWAP_ This swaps the top two members of the stack, it allows us to access the 2nd member for some purpose, and the access the top member. SWAP is given the `$` character, which looks a bit like an S for SWAP

_OVER_ This copies the 2nd member of the stack to the top of the stack, leapfrogging the previous top member. In MINT we use `%` to represent OVER. It suggests o being copied and jumping over the / to appear as the new top of the stack, giving us o / o

So when do we use these stack operations?

We use _DUP_ when we need to use the top item twice. If we put a number on the stack and want to square it (multiply it by itself) we use DUP to make a copy followed by a multiplication \*

```
5 " * .   <ENTER>
00025
>
```

DUPLICATE the 5 on the stack, multiply and print the answer. This is a very simple use case.

We use _DROP_ when there is a value on the top of the stack that we don't need. We can _DROP_ it and this exposes the 2nd member of the stack.

One example might be when we perform a DIVISION, but we are only interested in the REMAINDER (we are performing a MODULUS operation). We _DROP_ the QUOTIENT from the top of the stack, leaving the REMAINDER for us to use.

```
5000 15 / ' .  <ENTER>
00005
>
```

This will print the REMAINDER of 5000 divided by 15, which is 00005

_SWAP_ `$` is used to access the 2nd stack member, operate on it, and then still have the first member remaining on the stack. We will look at _SWAP_ again in Chapter 6 when we look at memory and variables.

In this short chapter we have looked at the stack operations, _DUP_, _DROP_, _SWAP_ and _OVER_.

Exercises:

Put the following numbers on the stack and try out the four basic stack manipulation operations. Use the Control P key `Ctrl P` to view the stack before and after the stack operation.

```
1234 5678 9999 1000 <ENTER>
```

Now we press `"` for _DUP_

```
> " <ENTER>
```

```
Ctrl P
> => 00001 01234 05678 09999 01000 01000
```

The last item has been DUPLICATED

Now use `'` for DROP followed by ENTER. Then use `Ctrl P` to examine the stack

```
> ' <ENTER>
Ctrl P
> => 00001 01234 05678 09999 01000
```

The last item 01000 has been DROPPED

Now use `%` for OVER and observe the result

```
> % <ENTER>
Ctrl P
> => 00001 01234 05678 09999 01000 09999
```

The 09999 has been copied and leapfrogged the 01000

Finally we will use SWAP `$` followed by `ENTER`

```
> $  <ENTER>
Ctrl P
> => 00001 01234 05678 09999 09999 01000
```

The 01000 and the 09999 have SWAPPED places.

Other, more complex, stack manipulations are possible, but for MINT these are the most commonly used.

Remember that we use `Ctrl P` to examine the contents of the stack

### Stack Operations

| Symbol | Description                                                                   | Effect       |
| ------ | ----------------------------------------------------------------------------- | ------------ |
| "      | duplicate the top member of the stack DUP                                     | a -- a a     |
| '      | drop the top member of the stack DROP                                         | a a -- a     |
| $      | swap the top 2 members of the stack SWAP                                      | a b -- b a   |
| %      | over - take the 2nd member of the stack and copy it onto the top of the stack | a b -- a b a |

In the next chapter we will introduce the most powerful feature of MINT, the means to DEFINE new User Operations

# Chapter 5

## Introducing User Definitions

In the last two chapters we have introduced the stack, basic arithmetic and stack manipulation operations. You have also encountered about one third of the commonly used functions used in MINT. These operations form the bread and butter of the MINT language, so before we go too much further it is important that you have a good grasp of the fundamentals.

In this chapter, we wish to introduce one of the most powerful programming techniques available within MINT. It is called the USER DEFINITION or COLON DEFINITION. It is one of the key mechanisms to making MINT a real programming language.

Remember as an infant how you probably played with LEGO, and from a few coloured blocks and a little imagination, you could build virtually anything, from houses, boats, trains and maybe spaceships.

Well the MINT USER DEFINITION is like a magic LEGO block, it can be any size, shape or colour you wish, entirely under your control. It will just take a bit of imagination and practice to get used to this new powerful concept.

In the last chapter we looked at stack manipulations _DUP_, DROP, OVER and SWAP.

I showed a snippet of MINT code DUP MULT DOT, that could be used to print out the SQUARE of a number. Well we can take this snippet of MINT code and turn it into one of our magic blocks.

First we have to give it a memorable name, and I will use uppercase S to remind me of the word SQUARE. We then have to enclose the magic "snippet" between a couple of characters, so we know where the definition starts and where it ends.

We use the COLON character `:` to start the definition, then the name `S`, followed by the code "snippet" and finally the SEMICOLON character `;` to end the definition.

```
:S " * . ;
```

There is one proviso, the NAME S must immediately follow the starting COLON. Other spaces have been left for clarity but are not needed. We can omit all the spaces thus

```
:S"*.;
```

How do we use this magic definition. Well by the miracle of substitution, every time the interpreter encounters `S` it will actually interpret the snippet `"*.` thus executing the sequence DUP MULT DOT

So we can put any number (up to about 255) onto the stack and immediately have MINT print out its square. Here we have the squares of 4,5,100 and 255 all of which use our newly defined S function

```
> :S"*.; <ENTER>
```

```
> 4S	 <ENTER>
00016
```

```
> 5S     <ENTER>
00025
```

```
> 100S   <ENTER>
10000
```

```
> 255S   <ENTER>
65025
>
```

Whilst this might be a trivial example, the COLON DEFINITION is immensely powerful and the principal means of extending the language.

Between the COLON and the SEMICOLON you may enter virtually any legitimate sequence of MINT command characters.

Here are a few examples:

```
:W`Hello World ` \N;
```

This prints the string Hello World that is contained between the back ticks and ads a NEWLINE character at the end using \N

If you type W four times it will print Hello World 4 times

```
> WWWW       <ENTER>
Hello World
Hello World
Hello World
Hello World

>
```

Sometimes there are useful pairs of characters that perform a useful function together. I will call these pairs "couplets" but that is not a strict definition.

There are cases that we want to ADD or SUBTRACT a small number from a parameter or variable. The following couples form useful commands

`1+` ADD 1

`1-` SUBTRACT 1

`2+` ADD 2

`2-` SUBTRACT 2

```
> 1234 1+ .  <ENTER>
01235
> 1234 1- .  <ENTER>
01233
> 1234 2+ .  <ENTER>
01236
> 1236 2- .  <ENTER>
01234
>
```

This has been a very brief introduction to the powerful COLON DEFINITION, which allows a sequence of MINT code to be substituted using just a single (capital letter) character.

In the next two chapters we will introduce more of the language, including conditional execution, loops, variables and arrays.

### User Definitions

| Symbol | Description                | Effect |
| ------ | -------------------------- | ------ |
| :      | define a new word DEF      | "C"    |
| ;      | end of user definition END |        |

# Chapter 6

## Conditional Code Execution and Loops

In the last chapter we introduced the COLON DEFINITION. It is now time to introduce some more of the built in command set to allow you to create a more colourful, rich language.

In Chapter 3 we introduced basic arithmetic operators, the likes of ADD, SUBTRACT, MULTIPLY and DIVIDE. There are times in programming where you just want to compare a couple of numbers, which is bigger? are they equal? etc. For this purpose MINT has three commands that we call COMPARISON OPERATORS.

We have three:

LESS THAN LT `<`

EQUAL TO EQ `=`

GREATER THAN GT `>`

These three operators COMPARE the top two entries on the stack and decide if a COMPARISON is TRUE or FALSE. If TRUE the number `1` is pushed onto the stack. If FALSE `0` is pushed onto the stack. The original parameters are removed from the stack.

Examples:

```
55 66 < .       <ENTER>
```

55 is less than 66 so 1 is placed on the stack.

```
195 34 > .      <ENTER>
```

195 is greater than 34 so 1 is placed on the stack.

If we reverse the order of the stack entries, then the FALSE condition will be put onto the stack.

If we attempt a GREATER THAN or LESS THAN comparison, but the numbers are EQUAL, then the FALSE condition 0 will be placed on the stack.

These COMPARISON operators allow a magnitude test to result in a simple BINARY result. The CONDITION is either TRUE or FALSE, 1 or 0.

This leads us on to the topic of CONDITIONAL EXECUTION. By this we mean, execute the following block of code if the condition is TRUE but skip it if the condition is FALSE.

How do we define the code block to conditionally execute? We use the familiar PARENTHESES or BRACKETS to enclose the conditional code block. The logical TRUE or FALSE determines whether the code between the brackets will be executed or not.

Here is an example using a pair of strings between back ticks
`This will be executed if TRUE`  
`This will be executed if FALSE`

```
5 4 > (`This will be executed if TRUE`)  <ENTER>
```

`This will be executed if TRUE`

```
5 4 < (`This will be executed if FALSE`) <ENTER>
```

`This will be executed if FALSE`

So as a GENERAL RULE if a code block within PARENTHESES is preceded by 1 it will be conditionally executed, if it is preceded by 0 it will not be executed. For example you might want to type a comment as follows:

0(Whatever is between these parentheses will never be executed)

The parentheses structure is quite powerful and allows us to execute CONDITIONAL code. But it has another couple of tricks up its sleeve, the ELSE function.

Often in programming we need to choose between two tasks to perform:

Do this, OTHERWISE Do that

We use the `\(` to execute the "Do that" function. the `\(` construct forms the command ELSE. For example

`(`do this`)\(`do that`)`

Or in a practical example, put two numbers on the stack say 55 32

```
55 32 > (`bigger`)\(`Smaller`)
```

## LOOPS

We have looked at conditional code execution based on the condition placed before the parentheses

`1(`execute only once`)`

`0 (`this will not be executed`)

But for LOOPS we want thing to execute many times. Fortunately MINT builds upon the conditional code mechanism to build an intuitive LOOP structure. We just need to increase the value that we place outside of the opening parentheses.

```
> 5(`This will print Hello World 5 times ` \N ) <ENTER>
This will print Hello World 5 times
This will print Hello World 5 times
This will print Hello World 5 times
This will print Hello World 5 times
This will print Hello World 5 times
>
```

LOOPS are a key structure of any programming language.

Inside the LOOP is a control variable known as the loop variable `i`. In the above example `i` starts at 5 and terminates the LOOP when it reaches zero. We can print out `i` during the loop, if you remember the old song "Ten Green Bottles":

```
> 10(\i@.` Green Bottles ` \N) <ENTER>
00000  Green Bottles
00001  Green Bottles
00002  Green Bottles
00003  Green Bottles
00004  Green Bottles
00005  Green Bottles
00006  Green Bottles
00007  Green Bottles
00008  Green Bottles
00009  Green Bottles

>

```

`i` is the loop variable which increments each time around the loop until it reaches the specified value, at which point the loop terminates.

The construct `\i@` is a means to push the `i` variable onto the stack and `\i@.` will print it out for each iteration around the loop. We will cover variables more in the next chapter.

We can create a very long loop, with up to 65525 iterations:

```
65535(\i@.\N) <ENTER>
```

This will print out all the decimal numbers between 0 and 65534 and will take many seconds to complete depending on the baud rate of your serial terminal interface.

Loops can be _NESTED_ create even longer structures:

```
1000(1000())`done`  <ENTER>
```

On a standard 4MHz Z80 system this "Million" LOOP will take about 70 seconds to execute before it returns with the `done` message.

This has only been a very brief introduction to COMPARISON OPERATORS, CONDITIONAL EXECUTION and LOOPING. These topics will be revisited in the second half of the manual that describes advanced programming techniques.

In this chapter you learnt about the Comparison Operators that return either TRUE 1 or FALSE 0 to the stack.

### Comparison Operators

| Symbol | Description          | Effect   |
| ------ | -------------------- | -------- |
| <      | 16-bit comparison LT | a b -- c |
| =      | 16 bit comparison EQ | a b -- c |
| >      | 16-bit comparison GT | a b -- c |

We also covered LOOPS and CONDITIONAL EXECUTION and introduced the LOOP VARIABLE \i@

### Loops and conditional execution

| Symbol | Description                                       | Effect |
| ------ | ------------------------------------------------- | ------ |
| (      | BEGIN a loop or conditionally executed code block | --     |
| )      | END a loop or conditionally executed code block   | --     |
| \\i    | returns index variable of current loop            | -- val |

In the next chapter we will look at User and System Variables.

# Chapter 7

## Variables

In the last chapter we looked at comparison operators, conditional execution and loops, but we also hinted at VARIABLES.

In MINT, a VARIABLE is a fixed location in memory where we can store a 16-bit number and retrieve it easily.

By way of convention, USER VARIABLES are defined by a single lower case alpha character. There are 26 of these, `a` to `z`.

There are really only 3 things we can do with a variable:

1. We can print out its address to see where the data is stored
2. We can read what is currently stored at that address using the FETCH command `@`
3. We can write a new value to the address using the STORE command `!`

Remember that variables are defined by lower case alpha characters `a` to `z`, and that character actually represents a numerical address in system RAM.

Lets look at the user variable `a`, we can easily find out its address in memory by typing

```
> a.	<ENTER>
35840
```

On my system variable `a` is stored at a two byte location 35840 and 35841

On my system I get the following response but this will vary depending on your system. The point to remember is that there are 26 user variables and they all are mapped to FIXED even addresses in RAM.

On power up the variable a may contain random data, we can read it using the following command:

```
> a@.	<ENTER>
65180
>
```

But we can initialise or SET a to a given value, let's say 05555

```
5555 a!    <ENTER>
```

Put 5555 on the stack and store it into variable a

Then we read it again to check this change has occurred:

```
> a@.		<ENTER>
05555
>
```

Read variable `a` onto the stack and print it out in decimal

Remember that we have 26 user variables `a` through to `z`.

More quick ways to use VARIABLES

`1234 a!` Store 1234 in the variable `a`

`5678 b!` Store 5678 in the variable `b`

`b@ .` Print the value stored in `b`

`a@ b@ + .` Add the contents of `a` to `b` and print the sum

`a@ b!` Copy the contents of `a` into `b`

`1a@ 1+ 1a!` Fetch the value in `a`, INCREMENT by 1 and store back in `a`

Variables are located at FIXED addresses and that address is defined by the lowercase letter we choose.

The stack can be used for temporary data but variables are for more long term storage. MINT is like a microprocessor with 26 registers, and we can choose which ones we wish to use.

VARIABLES give immense flexibility, and avoid us having to use a lot of tricky operations on the stack. For example we can read from `a` and copy to `b`

`a@ b!`

Or we can read `b` ADD 2 and store back to `b`

`b@ 2+ b!`

`a@ b@ + a!` ADD `a` and `b` and store back to `a` leaving `b` unaltered

In the next example we will combine VARIABLES and ADDITION within a LOOP to create the well known _Fibonacci Sequence_.

The sequence starts with 0 and then 1, and continues by adding the previous two terms together to create the next term. So the sequence `0 1 1 2 3 5 8 13 21 34` and so on is created. The sequence rapidly reaches a high value, so with 16-bit arithmetic it will rapidly approach the maximum number that can be handled.

To create a MINT program that will generate the sequence we first initialise a couple of variables `a` and `b` to 0 and 1 respectively and print them out:

```
0a! 1b! a@. b@.	<ENTER>
```

Then we define `F` as the Fibonacci sequencer enclosed within loop parentheses.

Variable `c` will hold the sum of the previous two entries. `a` and `b` are updated with each iteration of the loop

```
:F(a@b@+c!b@a!c@b!c@.);
```

As `F` defines the code sequence within LOOP parenthesis all we need is to put a parameter on the stack to control the number of times that the loop executes. In this case we have a maximum iteration count of 25.

```
0a! 1b! a@. b@. 25F	<ENTER>
```

Alternatively we could use

```
0a! 1b! 25(a@b@+c!b@a!c@b!c@.)	<ENTER>
```

The Fibonacci sequence in just 37 instructions.

The Fibonacci sequence may also be generated using clever stack manipulation only. Can you think of a way how to do this?

In this chapter we have learned the FETCH `@` and STORE `!` operations used to access variables stored in RAM

### Memory and Variable Operations

| Symbol | Description               | Effect     |
| ------ | ------------------------- | ---------- |
| @      | FETCH a value from memory | -- val     |
| !      | STORE a value to memory   | val adr -- |

There are many more things that we can do with variables and these will be covered in the second half of the manual where we learn some more advanced topics.

In this chapter we have learnt that there are 26 USER VARIABLES stored at fixed addresses. We have learned how to interrogate the address of the variable, FETCH a value and STORE a value to a VARIABLE.

In the next chapter we will look at ARRAYS, BYTE ARRAYS that store 8-bit values and WORD ARRAYS that hold 16-bit values.

# Chapter 8

## Arrays

Formal Definition:

An ARRAY is a structure in RAM that contains several elements either stored as BYTES or WORDS. It is uniquely defined by a POINTER and a LENGTH.

Let's start with an example. We have 16 random data values that we want to store in memory in such a way that we can easily access them later.

The best way to do this is to put them into an ARRAY. Lets assume that all the elements are 8-bit values, so we can put them into a BYTE ARRAY.

As it happens, these values all relate to the codes needed to drive a 7-segment HEXADECIMAL LED display. So I will define a User Command H that allows me to create an array:

```
:H\[235 40 205 173 46 167 231 41 239 47 111 230 195 236 199 71] $ a!;	<ENTER>
```

Here we have 16 8-bit decimal elements that are stored in an array as elements 0 to 15 (zero based). The array definition produces a LENGTH and a POINTER address on the stack so we SWAP these on the stack and store the pointer in variable a using $ a!

A BYTE array commences with `\[` but a WORD array commences with `[` Both arrays terminate with `]`

We could easily use the HEXADECIMAL number format to define this array, and it would look as follows, but be identical when stored in memory:

```
:H\[ #EB #28 #CD #AD #2E #A7 #E7 #29 #EF #2F #6F #E6 #C3 #EC #C7 #47 ] $a! ; <ENTER>
```

Having defined an array, we need to create some code to read and write to the individual members of the array.

It should be remembered that the first member is actually the zeroth!

If we type `H ENTER` the array will be initialised and the elements will be stored in consecutive bytes in memory.

We know that our variable a, acts as a pointer to the address in RAM where the start of the array begins.

We can examine this start address with this snippet

```
a@.   <ENTER>
```

On my system, this will give an arbitrary address somewhere in RAM, but this location will differ for all systems.

```
> a@.	<ENTER>
36034
```

So we know the zeroth element of the array is stored at address 36034. We can then examine the contents of this address using the byte-fetch instruction which is `\@`

Putting the sequence together we have `a@\@.`

```
> a@\@.	<ENTER>
00232
```

We can define `R` (for read) which will read the nth member of the array.

```
:R@+\@;  <ENTER>
```

read the nth element of a byte array

The way this is used is `0aR.` which will read and print the 0th member of the array, and as expected is 00232

```
> :R@+\@;
> 0aR.		<ENTER>
00232
>
```

To complement `R` we have `W` for WRITE, which will write to the nth member of the array.

```
:W@+\!;		<ENTER>
```

Write to the nth member of a byte array:

```
123 1aW     <ENTER>
```

Write 123 to member 1 of the array.

Remember that byte arrays are begun with `\[` and end with `]`

`\@` is the byte-FETCH instruction and `\!` is the byte-STORE instruction.

### WORD ARRAYS

These are very similar to byte-arrays but they are used to store 16-bit wide numbers.

Word arrays are begun with `[` and end with `]`

When we read or write to a word array, we must multiply the index by two so that it creates a 16-bit word address.

We can automate this process by defining a couple of words:

Define a word `R` that reads the nth member of the array:

```
:R @ $ {+ @ ;      <ENTER>
```

Example use `5aR` read the 5th word in the array `a`

Define a word `W` that writes to the nth member of the array:

```
:W @ $ {+ ! ;      <ENTER>
```

Example use `1234 6aW` write 1234 to the 6th word in the array `a`

In the examples above the couplet `{+` takes the top number off the stack, doubles it and adds it to the next stack element. This effectively multiplies our index by two before adding it to that array start address.

In this chapter we have looked at byte arrays and word arrays. We have shown how to define a byte array or a word array and allocate it to a user variable (lower case letter).

We have created a couple of simple words that allow reading or writing to the nth element of the array.

| Symbol | Description                   | Effect        |
| ------ | ----------------------------- | ------------- |
| \\@    | FETCH a byte from memory      | -- val        |
| \\!    | STORE a byte to memory        | val adr --    |
| [      | begin an array definition     | --            |
| ]      | end an array definition       | -- adr nwords |
| \\[    | begin a byte array definition | --            |
| \\]    | end a byte array definition   | -- adr nbytes |

# Chapter 9

## Boolean and Logical Operations

In the last 4 chapters we have introduced a variety of concepts using MINT, and the principal symbols needed to write MINT code.

In this chapter we would like to introduce a few more characters, which we have not yet explored.

### BOOLEAN LOGIC

Most computer languages have the means to perform bitwise BOOLEAN operations such as AND, OR and XOR (Exclusive OR). MINT also has these operators:

`&` Perform a bitwise AND on the top 2 elements of the stack and place the result on the stack.

`|` Perform a bitwise OR on the top 2 elements of the stack and place the result on the stack.

`^` Perform a bitwise XOR on the top 2 elements of the stack and place the result on the stack.

AND is frequently used to select certain bits from within the 16-bit word.

For example, if we are only interested in the bottom 4 bits of a larger word stored in the variable a we can perform a Boolean AND with decimal 15. In binary notation decimal 15 is written as 0000000000001111 where only the bottom 4 bits are set to logic 1. The effect of this will only select the bits where logic 1 is present both in variable a, and the logic "MASK"

```
a@ 15 & a!   <ENTER>
```

Select only the bottom 4 bits of `a`, setting all other bits to zero.

Boolean OR is used to force bits to logic 1, if the equivalent bit is set in the MASK

```
a@ 32 | a!    <ENTER>
```

Force bit 4 of variable a to logic 1.

Boolean XOR is used to invert bits if the equivalent bit is set in the MASK

```
a@ 64 ^ a!    <ENTER>
```

Invert bit 6 of variable a and store back into a.

If we want to flip all the bits in a word, we use the BOOLEAN INVERT operator which is the ~ character

```
32 ~ .
65503
>
```

The same result would be achieved by XORing the value with 65525

```
32 65535 ^ .
65503
>
```

Boolean operations are frequently used for manipulating the individual bits contained in the word, to extract sub-fields or to change the logic of specific bits.

### SHIFT OPERATIONS

Sometimes we wish to multiply or divide a number by two, or a power of two. For this we have the LEFT SHIFT and the RIGHT SHIFT operators `{` and `}`

```
{.       <ENTER>
```

Multiply the top of the stack by two using the LEFT SHIFT operator `{`

```
}}}}.    <ENTER>
```

Divide the top of the stack by 16 using the RIGHT SHIFT operator `}` four times

### Logical Operators

| Symbol | Description                          | Effect   |
| ------ | ------------------------------------ | -------- |
| ~      | 16-bit bitwise inversion INV         | a -- b   |
| \_     | 16-bit negation (2's complement) NEG | a -- b   |
| &      | 16-bit bitwise AND                   | a b -- c |
| \|     | 16-bit bitwise OR                    | a b -- c |
| ^      | 16-bit bitwise XOR                   | a b -- c |
| {      | shift the number to the left (2\*)   | a -- b   |
| }      | shift the number to the right (2/)   | a -- b   |

Note: logical NOT can be achieved with 0=

# Chapter 10

## Extended Operations and Alternate Commands

## Glossary

### LIST OF PRIMITIVES

Mint is a bytecode interpreter - this means that all of its instructions are 1 byte long. However, the choice of instruction uses printable ASCII characters, as a human readable alternative to assembly language. The interpreter handles 16-bit integers and addresses which is sufficient for small applications running on an 8-bit cpu.

### Maths Operators

| Symbol | Description                               | Effect   |
| ------ | ----------------------------------------- | -------- |
| -      | 16-bit integer subtraction SUB            | a b -- c |
| {      | shift the number to the left (2\*)        | a -- b   |
| }      | shift the number to the right (2/)        | a -- b   |
| /      | 16-bit by 8-bit division DIV              | a b -- c |
| \_     | 16-bit negation (2's complement) NEG      | a -- b   |
| \*     | 8-bit by 8-bit integer multiplication MUL | a b -- c |
| \\\_   | sign of number                            | n -- b   |
| \>     | 16-bit comparison GT                      | a b -- c |
| +      | 16-bit integer addition ADD               | a b -- c |
| <      | 16-bit comparison LT                      | a b -- c |
| =      | 16 bit comparison EQ                      | a b -- c |

### Logical Operators

| Symbol | Description                  | Effect   |
| ------ | ---------------------------- | -------- |
| \|     | 16-bit bitwise OR            | a b -- c |
| &      | 16-bit bitwise AND           | a b -- c |
| ^      | 16-bit bitwise XOR           | a b -- c |
| ~      | 16-bit bitwise inversion INV | a -- b   |

Note: logical NOT can be achieved with 0=

### Stack Operations

| Symbol | Description                                                          | Effect         |
| ------ | -------------------------------------------------------------------- | -------------- |
| '      | drop the top member of the stack DROP                                | a a -- a       |
| "      | duplicate the top member of the stack DUP                            | a -- a a       |
| \\D    | returns the depth of the stack                                       | -- n           |
| \\R    | rotate the top 2 members of the stack ROT                            | a b c -- b c a |
| %      | over - take the 2nd member of the stack and copy to top of the stack | a b -- a b a   |
| $      | swap the top 2 members of the stack SWAP                             | a b -- b a     |

### Input & Output Operations

| Symbol | Description                                               | Effect      |
| ------ | --------------------------------------------------------- | ----------- |
| ,      | print the number on the stack as a hexadecimal            | a --        |
| .      | print the top member of the stack as a decimal number DOT | a --        |
| \\E    | emits a char to output                                    | val --      |
| \\I    | input from a I/O port                                     | port -- val |
| \\K    | read a char from input                                    | -- val      |
| \\N    | prints a CRLF to output                                   | --          |
| \\O    | output to an I/O port                                     | val port -- |
| \\P    | non-destructively prints stack                            | --          |
| \\Z    | print definition by number                                | n --        |
| \`     | print the literal string between \` and \`                | --          |
| #      | the following number is in hexadecimal                    | a --        |

### User Definitions

| Symbol  | Description                | Effect |
| ------- | -------------------------- | ------ |
| ;       | end of user definition END |        |
| :<CHAR> | define a new word DEF      |        |
| ?<CHAR> | get the address of the def | -- adr |
| \{      | enter group NUM            | num -- |
| \}      | exit group                 | --     |

NOTE:
<CHAR> is an uppercase letter immediately following operation which is the name of the definition
<NUM> is the group number. There are currently 5 groups numbered 0 - 4

### Loops and conditional execution

| Symbol | Description                                       | Effect |
| ------ | ------------------------------------------------- | ------ |
| (      | BEGIN a loop or conditionally executed code block | n --   |
| )      | END a loop or conditionally executed code block   | --     |
| \\(    | beginIFTE \\(`true`)(`false`)                     | b --   |
| \\B    | if true break out of loop                         | b --   |
| \\i    | loop counter variable                             | -- adr |
| \\j    | outer loop counter variable                       | -- adr |

### Memory and Variable Operations

| Symbol | Description                                 | Effect        |
| ------ | ------------------------------------------- | ------------- |
| !      | STORE a value to memory                     | val adr --    |
| [      | begin an array definition                   | --            |
| ]      | end an array definition                     | -- adr nwords |
| @      | FETCH a value from memory                   | -- val        |
| \\!    | STORE a byte to memory                      | val adr --    |
| \\[    | begin a byte array definition               | --            |
| \\@    | FETCH a byte from memory                    | -- val        |
| \\+    | increments variable at address by an amount | val adr --    |

### System Variables

| Symbol | Description                          | Effect |
| ------ | ------------------------------------ | ------ |
| \\a    | data stack start variable            | -- adr |
| \\b    | base16 flag variable                 | -- adr |
| \\c    | text input buffer pointer variable   | -- adr |
| \\d    | start of user definitions            | -- adr |
| \\h    | heap pointer variable                | -- adr |
| \\i    | See: Loops and conditional execution | -- adr |
| \\j    | See: Loops and conditional execution | -- adr |

### Miscellaneous

| Symbol | Description                                   | Effect   |
| ------ | --------------------------------------------- | -------- |
| \\\\   | comment text, skips reading until end of line | --       |
| \\G    | execute mint code at address                  | adr -- ? |
| \\Q    | quits from Mint interpreter                   | --       |
| \\X    | execute machine code at address               | adr -- ? |

### Control keys

| Symbol | Description                     |
| ------ | ------------------------------- |
| ^B     | toggle base decimal/hexadecimal |
| ^E     | edit a definition               |
| ^H     | backspace                       |
| ^J     | re-edit                         |
| ^L     | list definitions                |
| ^P     | print stack                     |
