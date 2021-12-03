## Reduce

```
            \\ (a b -- x )
:M %%        \\ a b a b
-            \\ a b a>b 

\\ a b c --  c b a
        ssaddasdasdasdasdasdasds
pop hl        \\ a b    hl = c  
pop de        \\ a      de = b
ex (sp),hl    \\ c      hl = a
push de       \\ c b 
push hl       \\ c b a