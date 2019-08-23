# Tinki* Language Manual

This document describes the syntax and semantics of the language designed as part of the course Compilers(CSE419). To design out language we researched grammars of a multitude of programming languages such a Python, Decaf and C. This language has loosely been based on Decaf and C with a few modifications


## Syntax

In this section we'll proceed to formally define the macro and micro syntax of our language. We'll assume a Top Down approach where we first describe the macro syntax and then describe the micro syntax using context free grammar and regexes.


## Macro Syntax

```
<program> -> class Program '{' <var_decl>* <method_decl>* '}'

<field_decl> -> <type> {<id>
                | <id>'['<int_literal>']'
                | <id>'['<int_literal>']['<int_literal>']'}

<var_decl> -> {<type> | <type>'['<int_literal>']' | void} <id>([{<type> <id> {'['<int_literal>']' | '['<int_literal>']['<int_literal>']' }}+]) <block>

<block> -> '{' <var_decl>* <statement>* '}'

<type> -> int | uint | bool | char

<statement> -> <location> '=' <expr>
              | <method_call>
              | if(<expr>) <block> [else <block>]
              | <expr> ? <expr> : <expr>
              | while(<expr>) <block>
              | for(<id>=<expr>; [<expr>]; [<expr>]) <block>
              | return [<expr>]
              | break
              | continue
              | <block>

<method_call> -> callout(<string_literal> [,<callout_arg>+])

<location> -> <id>
              | <id>'['<expr>']'
              | <id>'['<expr>']['<expr>']'

<expr> -> <location>
        | <method_call>
        | <literal>
        | <expr> <bin_op> <expr>
        | - <expr>
        | ! <expr>
        | (<expr>)

<callout_arg> -> <expr> | <string_literal>

<bin_op> -> <arith_op> | <rel_op> | <eq_op> | <cond_op>

```
## Microsyntax

| RegEx | Token |
|:------:|:------:|
| ".*"  | <string_literal> |
| '.' \| EOF  | <char_literal> |
| true\|false  |  <bool_literal> |  //how do you ensure truefalse, true_, etc are allowed identifiers
| [0-9]+   | <int_literal>  | // do we need to include spaces on either side for flex to parse?
| [a-zA-Z][a-zA-Z0-9]* | \<id> |
| == \| !=   |  <eq_op> |
| <= \| >= \| < \| >  |   <rel_op> |
| [\\+ \\- \\* / %] | <arith_op> |
| \|\||&& | <cond_op> |
| \+= \| \-= \| = | <assign_op> |
| int \| boolean \| char | \<type>  | //Oh no, decaf doesn't support chars and strings at alll!!

## Semantic Checks
It is possible for statements to be produced by the given grammar, and therefore be completely syntactically correct, but still be useless, meaningless or wrong. This is the semantic aspect of the program.

While it is not possible to eliminate all symantic errors at compile time, a few can be taken care of:

### Type Mismatch
We check every assignment statement to ensure that the assigned data types to identifiers or literals on both sides match.

### Undeclared Variable
Ensure that every identifier in use has been assigned a type.

### Redeclaring Variables
Redeclaring variables, especially if they already contain data is not allowed.

### Accessing a variable out of scope
The compiler would need to keep tab of variables declared in given environments

### Method Call Arguments and Method Parameter Mismatch
Compiler checks every method call against method definition.
