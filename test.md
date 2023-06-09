# Context

The following is a compilation of several LaTeX rendering bugs I encountered while converting a textbook from LaTeX to Github Markdown.
I will also list potential workarounds, provided I have any.
Hopefully this document becomes and more normal as these bugs are fixed.

# Issues related to inline math mode (single dollar signs)

## Issue 1

### Description

Sometimes the renderer doesn't render inline math mode LaTeX if there are more than one such modes used.
This also causes a slight spacing issue and can also eat input in the same line.

### Example(s)/Discussion

If two inline math mode usages _within the same line_ have no commands, such as: $a$ and $b$, it is rendered correctly.

If the second math mode contains a command, such as $a$ and $\alpha$,
then the space before the $a$, as well as the characters " and " get eaten and the alpha character is not rendered.

To demonstrate how much gets eaten and how, here is how the **two lines above** look like if I combine them into one line.

If the second math mode contains a command, such as $a$ and $\alpha$, then the space before the $a$, as well as the characters " and " get eaten and the alpha character is not rendered.

### Workaround

The **math command** itself can be rendered be fixed by adding a space before the **first** offending math mode.
For example: $a $ and $\alpha$.
Notice how this does not fix the spacing issue however. For that, if we add an additional space before the very first dollar sign (so there are two additional spaces total), this fixes the spacing issue.
For example: $a $ and $\alpha$.

In a given paragraph, it is not always straightforward to see which math modes have to be fixed. Take the following excerpt, which contains numerous inline math modes, but only some of them needed to be altered to display correctly, and curiously, the first fix needed was far from a command.

This can be seen by looking at $(a+b)^ n$ as a product of $n$ parentheses
and multiply these by picking one item ($a$ or $b$) from each. If we picked
$a$ from $x $ parentheses and  $b $ from $(n-x)$, then the product is
$a^x b^{n-x}$. We can choose the $x $ $a$'s in a total of $\binom{n}{x}$
ways so the coefficient of $a^x b^{n-x} $ is $\binom{n}{x}$.

**_NOTE:_** It is not advisable to naively add spaces to the beginning and end of all inline math modes because of the next issue.

## Issue 2

### Description

If there is a space at the end of an inline math mode, and there are any further characters, then content will not always render.

### Example(s)/Discussion

Two examples, each on their own line, are below:
$\mathbb{N} $ foo
$\cdot $ bar
where the expected outputs are
$\mathbb{N}$ foo
and
$\cdot$ bar
respectively.
Note that the offending inline math mode does not have to _end_ with a command, for example:
$3^2=3\cdot 3 $ foo.
However, this issue does **not** seem to occur if the inline math mode contains no commands. For example:
$3 $ bar.

### Workaround

Either don't end inline math modes with spaces or have a newline after each inline math mode.

## Issue 3

### Description

Inline math mode doesn't render if it contains a newline character.

### Example(s)/Discussion

It's easy to imagine long commands coming about when writing, but a small example is $a
b$,
where the expected output is $ab$.

### Workaround

Make sure there are no line-breaks in inline math commands.

**_NOTE:_** It is clear that if there is more than a **_single concurrent_** line-break, then the command should not render, as in normal LaTeX.

## Issue 4

### Description

Two underscore characters in (even in seperate math modes!) can be interpreted as metacharacters in certain circumstances, such as when the underscores are surrounded by non-alphabetic characters.

### Example(s)/Discussion

A minimal example is:
$a)_0 b_+$

Here, it is important that the bracket and the plus (the "bounding" characters) be non-alphabetic characters.

A more natural occurence, that shows that the underscore is italicizing things (and un-italicizing when it reaches the next compatible underscore), as well as how this can impact multiple math modes (even not on separate lines) is:

"If $(a_n)_{n\in\mathbb{N}}$ is convergent
to $\lim_{n\to\infty}s_n$, then we write..."

**_NOTE:_** This can even prevent rendering of a centered math mode, e.g. if a paragraph ended on one.

### Workaround

In certain cases, it might be possible to add a spaces, such as _after the first underline_. For example:
$n)_ 0$ foo bar $\lim_{x}$

## Issue 5

### Description

A period cannot follow inline math mode that ends in a period.

### Example(s)/Discussion

This was found with $n=0,1,2,...$.
but even $n.$. is enough to prevent rendering.

### Workaround

Introduce a space _before_ the period outside of the math mode.
This changes the output to $n=0,1,2,...$ .

# Issues related to centered math mode (two dollar signs)

## Issue 6

### Description

Centered math mode that contains a linebreak can fail to render if there is other math previously in the same paragraph.

### Example(s)/Discussion

Here is some arbitrary math:
$abc$
and next we have a centered math mode containing a linebreak that will not render:

$$
ab =
ba
$$

We observe the same behaviour even if the previous math is _centered_ math, but in the same paragraph:
$$abc$$
and next we have a centered math mode containing a linebreak that will not render:

$$
ab =
ba
$$

### Workaround

One way around this is to not have line-breaks in centered math, but this can get very messy with e.g. matrices, tables, etc. A somewhat safer approach is to isolate all centered math modes, such that there is an empty line both before and after centered math mode.

## Issue 7

### Description

Centered math can fail to render if there is _any_ preceding text or symbols in the same paragraph, even if the math mode starts at the beginning of the next line in the paragraph.

### Example(s)/Discussion

Compare these two situations, which do not run
Text

$$
\begin{array}{cc}
0 & 1
\end{array}
$$

,.!?+-

$$
\begin{bmatrix}
0 & 1
\end{bmatrix}
$$

with

$$
\begin{array}{cc}
0 & 1
\end{array}
$$

and

$$
\begin{bmatrix}
0 & 1
\end{bmatrix}
$$

### Workaround

Isolate centered math mode with empty lines before and after.

# General issues

## Issue 8

### Description

The summation operator, with limits, does not render correctly in a _inline_ math mode.

### Example(s)/Discussion

If I write in LaTeX e.g. the sum from $1$ to $n$ using the **sum** command, the operator is situated above _both_ limits.
Example: $\sum_1^n$.

### Workaround

It is possible to use the centered math mode to obtain a correct summation sign:
$$\sum_1^n$$
If this does not make sense with one's text, it is also possible to use the **Sigma** command instead of **sum**.
For example: $\Sigma_1^n$.
**_Note:_** This workaround should not be used in centered math mode, as that produces less desirable results.
$$\Sigma_1^n$$

## Issue 9

### Description

A backslash is not being correctly read when in inline math mode.

### Example(s)/Discussion

Here is an example using a literal curly bracket, which does not render correctly,

$$\{ x\in \mathbb{R}: x>0 \}$$

and if we imagine a larger example, where the author wanted to use the \left and \right functions, then it can prevent rendering altogether:

$$\left\{ x\in \mathbb{R}: x>0 \right\}$$

Here is another, where the hashtag symbol is not recognized

$$\# \text{People}$$

### Workaround

If the single backslash backslash is replaced by a **double backslash**, then the LaTeX renderer gets what it's supposed to get and the result is rendered.

$$\\{ x\in \mathbb{R}: x>0 \\}$$

$$\\# \text{People}$$

## Issue 10

### Description

If a letter appears directly after the "less than" operator, then rendering fails.

### Example(s)/Discussion

$x$ greater than zero should be able to be writen as $0<x$.

$$0<x$$

While if we replace $x$ with a number, it renders correctly: $0<1$.

### Workaround

Adding a space _direcly after_ the "less than" operator makes is render: $0< x$.

## Issue 11

### Description

The asterisk character behaves oddly with the superscript operator.

### Example(s)/Discussion

While it will sometimes render correctly, such as:

$$
t^{*}
$$

if we place it in an environment whose name contains an asterisk, it does not render, seemingly as if the final asterisk was not there.

$$
\begin{align*}
t^{*}
\end{align*}
$$

The issue can also arise if there is another asterisk, but not in an environment name.
Here is a snippet from a large equation that necessitated being on two lines:

$$
t^*_{1-\alpha,n+m-2} \\
-t^*_{1-\alpha,n+m-2}
$$

### Workaround

Use "\ast" instead of the asterisk character, apart from when it is part of the name of an environment.

## Issue 12

### Description

When in either inline or centered math mode, if there is an equals sign on its own line, then it is interpreted as a markdown meta-character.

### Example(s)/Discussion

Here are the results when using both inline and centered math modes:

# $ab

ba$

$$
ab
=
ba
$$

### Workaround

For the centered math mode, moving the final $b$ so it in the same line as the equals sign causes correct rendering:

$$
ab
=b
a
$$

The same is not true if the same is done with the inline version, which results in this:

$ab
=b
a$

but in that case it is possible to delete newlines until the correct result is obtained:

$ab=ba$

## Issue 13

### Description

Certain envirornments don't expand to multiple lines properly unless the markdown itself also contains newlines as well.

### Example(s)/Discussion

Here is an example using inline math

$f(x,y) = \begin{cases} 2 & x \leq 1\\ 0 & \text{otherwise} \end{cases}$

and even using centered math mode doesn't fix the issue:

$$f(x,y) = \begin{cases} 2 & x \leq 1\\ 0 & \text{otherwise} \end{cases}$$

### Workaround

If we manually add the rquired newlines, then we get:

$$
f(x,y) = \begin{cases} 2 & x \leq 1\\
0 & \text{otherwise} \end{cases}
$$

Which works well in this case, but could be more akward if one wanted to make an inline sentence with e.g. vectors.
