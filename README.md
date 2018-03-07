# 6lang/Perl 6 Golfing Cheatsheet

As I'm getting bored with code golf, I decided to share some of the
tricks with the world. If that document makes golfing less fun for
you, you can close your eyes while reading it.

**Please note:** This document is mostly focused on golf platforms that
count codepoints instead of UTF-8 bytes. That said, I will try to
include unicode-unrelated tricks too.

**Also:** This document does not list *everything* and probably it
  never will. Contributions are welcome.

## Numbers

### Unicode
If one of the numbers that you are using is one of these:
```
NaN -0.5 0.00625 0.025 0.0375 0.05 0.0625 0.083333 0.1 0.111111 0.125
0.142857 0.15 0.166667 0.1875 0.2 0.25 0.333333 0.375 0.4 0.416667 0.5
0.583333 0.6 0.625 0.666667 0.75 0.8 0.833333 0.875 0.916667 1 1.5 2
2.5 3 3.5 4 4.5 5 5.5 6 6.5 7 7.5 8 8.5 9 10 11 12 13 14 15 16 17 18
19 20 21 22 23 24 25 26 27 28 29 30 31 32 33 34 35 36 37 38 39 40 41
42 43 44 45 46 47 48 49 50 60 70 80 90 100 200 300 400 500 600 700 800
900 1000 2000 3000 4000 5000 6000 7000 8000 9000 10000 20000 30000
40000 50000 60000 70000 80000 90000 100000 200000 216000 300000 400000
432000 500000 600000 700000 800000 900000 1000000 100000000
10000000000 1000000000000

# perl6 -e 'for ^0x10FFFF { .unival.say }' | sort -un
```

Then use the corresponding unicode character.
```perl6
say ㊿
#   50

say 兆
#   1000000000000
```


## Unicode numeric literals to avoid unneeded whitespace
```perl6
say 2 xx 3
say ②xx③

say 2,4 Z+7,8;
say 2,④Z+7,8;

say $_/2 for ^10;
say $_/②for ^10;
```


### Topic variable increment
```perl6
say $_++
```
```perl6
say .++
```


### Topic variable numification
```perl6
say +$_
```
```perl6
say .¹
```


## Strings

### Uninames

Use uninames, error messages and other built-in strings instead of
string literals:
```perl6
say (‘♔’…‘♙’)».uniname».words»[2]
#   KING QUEEN ROOK BISHOP KNIGHT PAWN
```

### Newlines

The shortest way to write a newline is by using an actual newline in the source code:
```perl6
say “X\nY”;
say “X
Y”;
```


## Lists

The « » word quoting operator can be used to create lists without separating items with commas:
```perl6
my ($a, $b, $c) = 42, 52, 62;
my$z=«25$a$b$c»
#    (25,$a,$b,$c)
```

Unquoted and quoted strings can be mixed:
```perl6
my$z=«a' 'b»
#    ("a", " ", "b")
```


## Loops


### `xx`
This one is a bit tough to get into your code, but it does help
sometimes:

```perl6
say(42)for ^10
say(42)xx⑤
```

### Using sequences

This will produce warnings (Useless use of … in sink context), but it's OK if the golfing platform ignores stderr:
```perl6
.say for 1,{$_~0}…10000;
1,{.say;$_~0}…10000;
```

### Using »

Note that » is supposed to run stuff in parallel, so the order of execution is not guaranteed. In normal code you should not do ``<a b c>».say`` even though current Rakudo does the processing sequentially. But in code golf that's a nice trick:

```perl6
.say for <a b c>;
<a b c>».say;
```

### Autothreading
Sometimes `Junctions` cause subs to be called multiple times. This can be used to avoid for loops:

```perl6
put <a b c d>.any;
.put for <a b c d>;
```

## Conditionals

### Use junctions if possible

```perl6
say 42 if 0||1
```
```perl6
say 42 if 0|1
```

Note that you can use junctions as sub args, and you get a junction
back.


### 1-character if-then

```perl6
42==42&&say ‘hello’;
42==42>say ‘hello’;
42==42≠say ‘hello’;
```

**Note:** this also applies to operations with sets (i.e. ``1 ∈ … ∈ .say``).


## Variables


### Pre-defined variables

`$/` and `$!` are pre-defined variables that don't have to be declared with `my`. They're normally used for regex matches and exceptions but can receive arbitrary values. This is useful to save intermediate values for reuse:
```perl6
say ($!=2*$_), ' ', 2*$! for ^10;
```

They can also be used for helper functions:
```perl6
say ($/={$_*100})(2), ' ', $/(3);
```


### Anonymous state variable

```perl6
say ($++, ++$, $-=5, $×=2, $+^=1) for ^5;
# (0 1  -5  2 1)
# (1 2 -10  4 0)
# (2 3 -15  8 1)
# (3 4 -20 16 0)
# (4 5 -25 32 1)

# Factorial
say $×=++$ for ^10;
```


# Other

## Base 0x10FFFF

This techique is explained in more detail in [this post](https://perl6advent.wordpress.com/2017/12/23/day-23-the-wonders-of-perl6-golf/).

## infix .
```perl6
say (^50).max;
say ^50 .max;
```
