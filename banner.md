## Banner message display

- Sample message stored is a definition in X.
- The entry point is J which gets the address of X and passes it to a routine D.
- D writes 6 chars of the message to 6 byte buffer at d, converting it into 7 segment format along by using definition A.
- A converts ASCII chars to 7 segment (as best as it can) by using lookup table at c.
- J then calls I to scan the display 100 times before looping
- J loops this combination of D and I and bumps the starting point of the string each time. This creates the impression of the message scrolling across the displays (it only does it 6 times to start with but the number of times it happens could be increased to the length of the message)

```
\[ #00 #18 #00 #00 #00 #00 #00 #00 #00 #04 #10 #00 #00 #00 #00 #00 #EB #28 #CD #AD #2E #A7 #E7 #29 #EF #2F #00 #00 #00 #00 #00 #00 #00 #6F #E6 #C3 #EC #C7 #47 #E3 #6E #28 #E8 #CE #C2 #6B #6B #EB #4F #2F #43 #A7 #46 #EA #E0 #EA #6E  #AE #CD ]' c!

\[0 0 0 0 0 0]' d!
:XHELLO THERE ITS ME AGAIN!      ;

:A #20 - c@+ \@;
:B \@A$\!;
:C 1+$1+$;
:D 6(%%B C)'';

:E #40 |1\O;
:F \@2\O;
:G }$1+$;
:H \d@ #20 6(%%EF G)'' ;
:I 100(H) 0E;

:J d@ ?X 6(%% \i@+ DI)'';
```
