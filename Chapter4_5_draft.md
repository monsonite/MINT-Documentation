# Chapter 4

## Stack Operations

In Chapter 3 we looked at entering numbers (PARAMETERS) onto the stack in both DECIMAL and HEX formats and using them to perform basic arithmetic operations.  Using DOT and COMMA we showed how results could be printed out to the screen. In reality, we were using MINT to perform as a basic 4 function CALCULATOR.

Using a stack is a powerful concept, it has its origins in the 1920s and further popularised in the 1950s when electronic computers started to become available. It provides a convenient means to store temporary data prior to performing some operation.

The stack has a few operations that help us to keep it in order, and in this chapter we will be looking at the stack manipulation commands.  MINT uses 4 basic stack manipulation commands and they are essential for efficient use of the language.

Think of them as housekeeping for the stack - helping tp keep everything tidy and in the correct order prior to the calculation or other operation.

The four basic commands are DUP, DROP, OVER and SWAP:

DUP  Duplicate the top member of the stack, in mint we use the command " which suggests we have two identical items on the stack

DROP Throw away the top member of the stack exposing the 2nd member.  We use the single quote character ' to represent DROP

OVER This copies the 2nd member of the stack to the top of the stack, leapfrogging the previous top member.  In MINT we use % to represent OVER. It suggests o being copied and jumping over the / to appear as the new top of the stack, giving us o / o

SWAP This swaps the top two members of the stack, it allows us to access the 2nd member for some purpose, and the access the top member.  SWAP is given the $ character, which looks a bit like an S for SWAP

So when do we use these stack operations?

We use DUP when we need to use the top item twice. If we put a number on the stack and want to square it (multiply it by itself) we use DUP to make a copy followed by a multiplication

5 " * .      DUPLICATE the 5 on the stack, multiply and print the answer. This is a very simple use case.

We use DROP when there is a value on the top of the stack that we don't need.  We can DROP it and this exposes the 2nd member of the stack.

One example might be when we perform a DIVISION, but we are only interested in the REMAINDER (we are performing a MODULUS operation). We DROP the QUOTIENT from the top of the stack, leaving the REMAINDER for us to use.

5000 15 / ' .    This will print the REMAINDER of 5000 divided by 15, which is 00005

SWAP is ideal to access the 2nd stack member, use it and then still have the first member remaining on the stack. We will look at SWAP again in Chapter 6 when we look at memory and variables.

In this short chapter we have looked at the stack operations, DUP, DROP, OVER and Swap


Exercises:

Put the following numbers on the stack and try out the four basic stack manipulation operations. Use Control P to view the stack before and after the stack operation.


1234 5678 9999 1000

DUP

> "

> => 00001 01234 05678 09999 01000 01000    The last item has been DUPLICATED


Now use ' for DROP followed by ENTER. Then use Control P to examine the stack

> '

> => 00001 01234 05678 09999 01000      The last item 01000 has been DROPPED


Now use % for OVER and observe the result

> %

> => 00001 01234 05678 09999 01000 09999   The 09999 has been copied and leapfrogged the 01000

Finally we will use SWAP $ followed by ENTER

> $

> => 00001 01234 05678 09999 09999 01000   The 01000 and the 09999 have SWAPPED places.


Other, more complex, stack manipulations are possible, but for MINT these are the most commonly used.



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

In the last two chapters we have introduced the stack, basic arithmetic and stack manipulation operations.  You have also encountered about one third of the commonly used functions used in MINT. These operations form the bread and butter of the MINT language, so before we go too much further it is important that you have a good grasp of the fundamentals.

In this chapter, we wish to introduce one of the most powerful programming techniques available within MINT. It is called the USER DEFINITION or COLON DEFINITION. It is one of the key mechanisms to making MINT a real programming language.

Remember as an infant how you probably played with LEGO, and from a few coloured blocks and a little imagination, you could build virtually anything, from houses, boats, trains and maybe spaceships.

Well the MINT USER DEFINITION is like a magic LEGO block, it can be any size, shape or colour you wish, entirely under your control. It will just take a bit of imagination and practice to get used to this new powerful concept.

In the last chapter we  looked at stack manipulations DUP, DROP, OVER and SWAP.

I showed a snippet of MINT code DUP MULT DOT, that could be used to print out the SQUARE of a number.  Well we can take this snippet of MINT code and turn it into one of our magic blocks.  

First we have to give it a memorable name, and I will use uppercase S to remind me of the word SQUARE. We then have to enclose the magic "snippet" between a couple of characters, so we know where the definition starts and where it ends.

We use the COLON character : to start the definition, then the name S, followed by the code "snippet" and finally the SEMICOLON character ; to end the definition. 

:S " * . ;     

There is one proviso, the NAME S must immediately follow the starting COLON. Other spaces have been left for clarity but are not needed. We can omit all the spaces thus :S"*.;

How do we use this magic definition. Well by the miracle of substitution, everytime the interpreter encounters S it will actually interpret the snippet "*.  (DUP MULT DOT).

So we can put any number (up to about 255) onto the stack and immediately have MINT print out its square. Here we have the squares of 4,5,100 and 255 all of which use our newly defined S function

> :S"*.;

> 4S
00016
> 5S
00025
> 100S
10000
> 255S
65025
>


