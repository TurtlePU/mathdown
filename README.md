# mathdown


Mathdown is a lightweight markup language for writing math homeworks.

## Checklist

- [ ] regular text, paragraphs
- [ ] bold, italic, verbatim
- [ ] code blocks
    - [ ] language highlighting in LaTeX
- [ ] lists, nested lists
- [ ] images, links
    - [ ] complex image layout in Markdown
- [ ] math mode
    - [ ] unicode mapping
    - [ ] simple builtin macros
    - [ ] matrix macro
    - [ ] custom macros
- [ ] custom LaTeX prelude

## Compilation targets

1. Markdown + MathJax
2. HTML + MathJax
3. PDF via LaTeX

## Syntax

Text syntax is really close to markdown.

```
# Title

## Section

### Subsection

#### Subsubsection

##### Paragraph

Regular text

Another paragraph

1. Ordered
2. List

* Bullet
* List
    - nested
    - list
        1. another
        2. nested
        3. list
        content after the list

**Bold**, *italic*, ***italic bold***, `code`.

\`\`\`language
    code block
\`\`\`

![Image title (tex) / placeholder (web)](image.png)

[Link text](http://example.com)

[](http://example.com/show url look ma spaces)

Inline math expression: {sin x + 1/2 + 1/80 ({tg^100 x - a}/{9 b} + w)}

(Possibly multiline) blocks:

{
    mathbb{E}[{x!}/{sin x + cov}] = int_{-oo}^oo {x!}/{sin x + cov} p(x)  d x
}

Fallback to LaTeX:

{
    latex{
        \mathbb{E}\left[ \dfrac{x!}{\sin x + \operatorname{cov}} \right] =
            \int\limits_{-\infty}^\infty
                \dfrac{x!}{\sin x + \operatorname{cov}} p(x) \, dx
    }
}
```

## Math mode

The following rules describe translation into latex.

If a word consists of one alphabetic symbol, it is written in math font
(~ italic).

Otherwise it is wrapped in operatorname.

Unicode symbols are translated to their latex symbol equivalents, if any.

`w_x^y` are left as is for one-latin-letter `w` and intercalated with `\limits`
for operators and nonlatin `w`.

{} is used to group expressions and to call macros.

Macros are called with `macro_name{opt_arg}{opt_arg}{arg}{arg}...`. To use macro
name as is, prepend it with a backslash.

Note that there are macros without arguments, they are evaluated immediately
(e.g. `int` and `oo` above).

Parentheses, brackets and curly braces (`\{ \}`) are automatically prefixed with
`\left` and `\right`. To leave paren unpaired, use `{:` and `:}`.

Division (and maybe something else later) applies a heuristic to determine how
to display it: using either `/`, `\frac` or `\dfrac`. (Therefore it can be
thought of as infix macro, I'll think about the possibility of adding these.)

Two spaces = `\,`, three = `\;`, four = `\.`, quad an qquad are macros.

Also there should be a heuristic to determine general environment for math mode:
`\[ \]` or `multiline*`. (Is it possible to do elegantly at all?..)

## More macros

`matrix`: the only required argument is a sequence of expressions, ellipses or
vertical slashes split by spaces. Ellipses get transformed into various `\dots`
commands, slashes separate blocks. If slashes are present, sizes of blocks
should be passed in an optional argument. Asterisk means "free form".

By default, matrix is not in parentheses, simply wrap them in your preferred
parenthese kind.

In the following example, matrix consists of four blocks: top left is 1 row and
2 columns, top right is 1xM, bottom left is Nx2, bottom right is NxM.

```
{
    ( matrix{1,*;2,*}{ 1 2 | 3 ... 6 | 7 8 ... 11 12 | 13 ... 24 } )
}
```

In latex, the same is written as

```
{
    latex{
        \begin{pmatrix}
            \begin{matrix}
                1 & 2
            \end{matrix}
            & \rvline &
            \begin{pmatrix}
                3 & \cdots & 6
            \end{pmatrix}
            \\ \hline \\
            \begin{pmatrix}
                7 & 8 \\
                \vdots \\
                11 & 12
            \end{pmatrix}
            & \rvline &
            \begin{pmatrix}
                13 & \cdots & . \\
                \vdots & \ddots & \vdots \\
                . & \cdots & 24
            \end{pmatrix}
        \end{pmatrix}
    }
}
```

There should be also `vector` macro which is a convenience wrapper around
`matrix`.

## Your own macros

You can define your own macros. TODO: the actual way to do so :)

Variants:

1. Embedded simple Turing-complete language with S-expressions (LISP-like)
2. Embedded Lua / Python blocks provided with libraries
3. Separate importable Lua / Python file (+ libraries)
4. Whatever ad-hoc unreadable sh*t LaTeX macros are
