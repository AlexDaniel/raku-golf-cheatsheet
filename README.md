# Raku/Perl 6 Golfing Cheatsheet

This repo is a place to share some of the golfing
tricks with the world. If that document makes golfing less fun for
you, please feel free to close your eyes while reading it.

**Please note:** This document is mostly focused on golf platforms that
count codepoints instead of UTF-8 bytes. That said, unicode-unrelated
tricks are welcome too.

**Also:** This document does not list *everything* and probably it
  never will. Contributions are welcome.

## Unicode

### Unicode numeric literals
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

This can condense large numbers into single characters.

```perl6
say ㊿
#   50

say 𖭡
#   1000000000000
```

This can also be used to avoid excess whitespace before and after numbers:

```perl6
say 2 xx 3
say ②xx③

say 2,4 Z+7,8;
say 2,④Z+7,8;

say $_/2 for ^10;
say $_/②for ^10;
```

Additionally there's also some numerical constants, like `π` (pi), `τ` (tau) and `𝑒` (Euler's constant).

### Unicode operators

Some unicode characters double as operators, such as `²` (squared), `³` (cubed). A full list can be found at the [perl docs](https://docs.perl6.org/language/unicode_ascii). Some of the more useful ones are the set operators like `∅∈∉∪` etc, the sequence operator `…`, and the multiplication/division alternatives `×÷` to save whitespace.

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

### Quote words (< > and « »)

This allows you to create lists of strings/numbers without using quotes and commas. The elements are separated by whitespace however, so they can't contain whitespace themselves.

```perl6
say <1 2 3>
say <apple orange bannana>
```

The « » word quoting operator can be used to interpolate variables:
```perl6
my ($a, $b, $c) = 42, 52, 62;
say «25$a$b$c»;
say (25,$a,$b,$c);
```

Unquoted and quoted strings can be mixed:
```perl6
say «a' 'b»;
say ("a"," ","b");
```

Or `{ }` expressions:
```perl6
say «8{^9}0»;
say (8,|^9,0);
say «abc{"def"xx 9}ghi»;
say ("abc",|("def"xx 9),"ghi");
```

### Creating 2D lists

```perl6
say [1,2,3;4,5,6;7,8,9]
#   [(1 2 3) (4 5 6) (7 8 9)]
```

### Flattening 2D lists

```perl6
say [[1,2,3],[4,5,6]][*;*]   # Can remove whitespace afterwards
say [[1,2,3],[4,5,6]].flat
```

## Loops

### Using `xx`
This one is a bit tough to get into your code, but it does help sometimes:

```perl6
say($_)for ^5
say($++)xx⑤
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

You can also chain `»`s for diminishing returns.

```perl6
.uc.say for <a b c>;
<a b c>».uc».say;
```

If the function is more complicated, you can use `».&{}`:

```perl6
say +.comb for <aPpLe oraNGe Banana>;
<aPpLe oraNGe Banana>».&{say +.comb};
```

Keep in mind that `»` loops over for *all* values of multi-dimensional arrays and returns with the same structure, which could be of benefit.

```perl6
say [[1,2,3],[4,5],8]».succ
#   [[2 3 4] [5 6] 9]
```

This may depend on the nodality of the function, i.e. if the method takes a list.

```perl6
say [[1,2,3],[4,5],8]».elems
#   [3 2 1]
```

### Autothreading
Sometimes `Junctions` cause subs to be called multiple times. This can be used to avoid for loops:

```perl6
.put for <a b c d>;
put <a b c d>.any;
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

Junctions can also be used to terminate sequences with side effects:

```perl6
.say for 1…⑽
```
```perl6
1…*.say×*>⑽
```
```perl6
1…{.say}&⑽
```

If the block evaluates to a falsey value, a `|` or `^` juncation can be used instead.


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

Though if you are using the function more than a couple of times, then consider using a defined variable for one byte calls. This has a two byte overhead for `my`, but saves a byte for each call and an extra byte if it is at the end of a statement.
```perl6
$/={$_*100};say [$/(2),$/(3),$/(4)]
# Vs
my&f={$_*100};say [f(2),f(3),f 4]
```

If you're using `$/` to store a list, then you can use the special `$0,$1,$2 ...` variables to access specific elements without indexing.
```perl6
$/=(0,1,*+*...*);
say "$0 $1 $5 $7 $10";
#  0 1 5 13 55
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

### Topic variable (`$_`)

The `$_` variable is very useful. You can call a method on the `$_` variable without using the variable name, e.g. `.lc`. There are a few ways of doing this, the most useful of which is the `.&{}` operator. The smartmatch operator `~~` is shorter in some circumstances, but doesn't return the value.

```perl6
say .uc~.lc with 'aPpLe';
$_='aPpLe';say .uc~.lc;
say 'aPpLe'.&{.uc~.lc};
'aPpLe'~~say .uc~.lc;
```

#### Topic variable increment
```perl6
say $_++
```
```perl6
say .++
```

#### Topic variable numification
```perl6
say +$_
```
```perl6
say .¹
```

#### Topic variable indexing

```perl6
say .[0]
```

## Hyper Operators (« and »)

We've seen the `»` used for postfixes above, but there are some other uses for it and its counterpart `«`.

### Applying prefixes

```perl6
say -«[[0,1,2],[3,4],5]
#  [[0 -1 -2] [-3 -4] -5]
say ^«[[0,1,2],[3,4],5]
# [[^0 ^1 ^2] [^3 ^4] ^5]
```

### Zipping lists

Ordinarily you would use the `Z` operator to zip lists, but using `«»` allows you to zip lists of different lengths, with the shorter list cycling.
```perl6
say <1 2 3 4 5> «~»<a b>
#  (1a 2b 3a 4b 5a)
```
The direction of the hyper operators matter, with the arrows pointing at the shorter list in order to cycle:

```perl6
say <1 2 3 4 5> «~«<a b>;
#  (1a 2b)
```

## Other

### Base 0x110000

If the golf platform counts Unicode code points instead of bytes, you can
store large numbers in Unicode strings, yielding about 20 bits or 6 decimal
digits per code point. There are 0x110000 (decimal 1114112) code points in
total and you can encode a number into a base 0x110000 string like this:

```perl6
my $n = :10('1234567890' x 10);
say $n.polymod(1114112 xx *).reverse.chrs;
# Û𜭇󙐧񹂈𚷄򔁖𮸑𬩗򪮒򁻝򞩱􍵮񻗷𫀍𯇆􀰡
```

Then the string can be decoded with an overhead of 17 characters:

```perl6
say :1114112['Û𜭇󙐧񹂈𚷄򔁖𮸑𬩗򪮒򁻝򞩱􍵮񻗷𫀍𯇆􀰡󠫒'.ords]  # 38 chars
# 12345678901234567890...
```

If the resulting string contains invalid code points like surrogates or causes
problems because of NFC normalization, simply try a different base.
You can even use bases larger than 0x110000 if all "digits" happen to stay
below 0x110000. But you often get the same result or even save a character
with a 6-digit base like 999999.

### Joined code points

Similarly you can join the code points instead for a similar compression ratio. Though the compression rate goes down with more zeroes in the number.

```perl6
my $n = '1234567890' x 10;
say $n.comb(/\d ** 1..7 <!before 0> <?{$/ < 0x110000}>/).chrs;
# 𞉀󀨔񔙎󜁲򊩒𞉀󀨔񔙎󜁲򊩒𞉀󀨔񔙎󜁲򊩒𞉀Ồ
```

For an overhead of only 12 bytes (-1 if you don't have to numify it).
```perl6
say +[~] "𞉀󀨔񔙎󜁲򊩒𞉀󀨔񔙎󜁲򊩒𞉀󀨔񔙎󜁲򊩒𞉀Ồ".ords     # 32 chars
```

This has the same problem with accents and surrogate code points as the other method.

### infix .
```perl6
say (^50).max;
say ^50 .max;
```
