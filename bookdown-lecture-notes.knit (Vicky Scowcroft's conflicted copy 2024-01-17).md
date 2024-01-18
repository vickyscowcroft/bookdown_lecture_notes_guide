--- 
title: "Making Lecture Notes with Bookdown"
author: "Vicky Scowcroft"
date: "2024-01-17"
site: bookdown::bookdown_site
output: 
  bookdown::gitbook:
    #css: "mystyle.css"
    split_by: rmd
documentclass: book
bibliography: [book.bib, packages.bib]
csl: apj.csl
subparagraph: yes
link-citations: yes
github-repo: vickyscowcroft/bookdown_lecture_notes_guide
description: "Guide for creating html lecture notes using Bookdown"
---

<!--- For HTML Only --->


# Introduction {-}

This guide walks through how to create html lecture notes using **R**, **Markdown** and **Bookdown**. This format is preferred as LaTeX doesn't play nicely with accessibility tools like screen readers, especially for maths heavy content. 

## System Requirements {-}

To use bookdown, you will need to have R and R studio installed (perhaps R studio is not a requirement, but it makes things a lot easier). These can both be installed from the DDAT Self Service app on a Mac, or R can be downloaded directly from [here](https://cran.r-project.org/) and R Studio from [here](https://rstudio.com/products/rstudio/download/). You will also need [**pandoc**](https://pandoc.org/index.html). This may already be installed on your system as part of the Anaconda distribution.

The [**bookdown**](https://bookdown.org/yihui/bookdown/) package can be installed from CRAN or Github:


```r
install.packages("bookdown")
# or the development version
# devtools::install_github("rstudio/bookdown")
```


## Caveat {-}

The instructions in this guide are what worked for me, on a Mac using R version 4.2.1, R Studio version 2022.07.1 and pandoc version 2.18 (updated August 2022). This guide also assumes that you're familiar with using the command line.





<!--chapter:end:index.Rmd-->

# Getting started {#getting-started}

The quickest way to get started is to use the bookdown demo as a template. [This page](https://bookdown.org/yihui/bookdown/get-started.html) gives instructions for how to do this.

The [bookdown: Authoring Books and Technical Documents with R Markdown](https://bookdown.org/yihui/bookdown/) is the main documentation for bookdown. I won't repeat a lot of the information here. 

This guide will walk through the steps I used to convert my LaTeX lecture notes for PH40112 to the bookdown version [here](https://vickyscowcroft.github.io/PH40112_rmd/). The files used to create the PH40112 notes are available on [github](https://github.com/vickyscowcroft/PH40112_rmd) and you're welcome to use those as a starting point. 



<!--chapter:end:01-intro.Rmd-->

# Converting from LaTeX notes {#convert-latex}

## Converting tex files {#sec:con-tex}

If you already have LaTeX versions of your notes you can convert these to markdown using pandoc Pandoc won't create *perfect* versions of your notes - you'll most likely have to do a bit of tweaking, but it gets most of the way there. 

You can convert a tex file to Rmd via the command
```
pandoc -f latex -t markdown input-tex.tex -o output-md.Rmd
```
where ```input-tex.tex``` is the name of your tex file and ```output-md.Rmd``` is the name of your output Rmd file. 

:::fyi
Pandoc converts one tex file at a time. It doesn't understand how to process a "master" tex file with `input` or `insert` LaTeX commands. Each file will need to be processed separately.
:::

Batch conversion {#batch-conversion}
----------------

If you want to convert a batch of latex files you can script the process. 

Tidying up the Rmd file {#tidying}
----------------

Pandoc will have done most of the work in getting your tex file converted to markdown. However, depending on how complex the original file was, you may end up with some tidying to do. 

Things that typically will need fixing are:

 * Section etc labels - if you used underscores in any of your latex labels you should change these to '-' in the markdown file. The underscores confuse markdown. Section \@ref(sec:chapters) discusses sections, chapters, etc.
 * Figures - the default pandoc conversion is quite limited, so here we'll use a more adaptable figure environment. In practice its easiest to just copy-paste the example code in Chapter \@ref(sec:figures) and edit the relavent bits.
 * Equations - the equations themselves should be fine, but if you want to have equation numbers you'll need to do some tidying. See Chapter \@ref(sec:equations) for the syntax.
 * Tables - tables have a different format but it's pretty easy to convert a latex table using find/replace. See Chapter \@ref(sec:tables).
 * References - generally easy to fix. See Chapter \@ref(sec:referencing).
 
Notes with gaps {#sec:gaps}
----------------

**Think carefully about whether you want to provide notes with gaps.**  While they may serve a purpose when the students have a hard-copy of the notes in a live lecture, they will most likely be using your notes on a screen this year.

It is possible to create notes with gaps for things like equations etc for the students to fill in. To do this you need to add some extra code to the `index.Rmd` file:


````
```
{r setup, include=FALSE}
library(knitr)
knit_engines$set(asis = function(options) {
  if (options$echo && options$eval) knit_child(text = options$code)
})
```
````

Then to "hide" e.g. an equation but still keep the same numbering, edit your equation to the following format:

`````
\begin{equation}
```{asis, echo=FALSE}
\Delta s^2 = \Delta x_{1}^{2} + \Delta x_{2}^{2}
```
(\#eq:dist01)
\end{equation}
`````

Here the `asis` command and back-ticks wrap around **only** the equation itself, rather than the whole `equation` object. If you wrap the `asis` around the `\begin{equation}` and `\end{equation}` the equation numbering will disappear too. 

Using the `titlesec` package {#sec:titlesec}
----------------

If you're using the `titlesec` latex package you'll need to add an additional line to the yaml information on your index.Rmd file:

```r
subparagraph: yes
```


This should fix any compilation errors related to the `titlesec` package when you're producing a pdf output.

Source: [Stack Overflow](https://stackoverflow.com/questions/42916124/not-able-to-use-titlesec-with-markdown-and-pandoc)



<!--chapter:end:02-latex-conversion.Rmd-->

# A quick introduction to Markdown {#sec:intro-markdown}

## What is Markdown? {#sec:what-is-markdown}

> Markdown is a lightweight markup language that you can use to add formatting elements to plaintext text documents.
>
> [markdown.org](https://www.markdownguide.org/getting-started/)

You can create markdown documents in any text editor that can create plain text files. For making things in bookdown, I prefer to use the  [RStudio](https://www.rstudio.com/, "RStudio") IDE so I have access to the build tools while I'm writing. 

## Markdown syntax {#sec:markdown-syntax}

You may already be familiar with some/all of the markdown syntax from using things like Moodle. A nice markdown cheat sheet can be found [here](https://en.support.wordpress.com/markdown-quick-reference/, "markdown cheatsheet").

These are some of the features I use most frequently. In the following examples, the grey boxes with mono-spaced font show the markdown and the green boxes show the rendered output. The "\\" you see in the raw markdown is an escape character to render the \#, \* etc. as symbols rather than a formatter. 

### Headings {#sec:heading-syntax}

You can make different levels of headings using different numbers of \#'s at the start of a line
```markdown
# One \#  for a chapter 
## Two \#\# for a section 
### Three \#\#\# for a subsection
```
:::showoutput
:::showh1
Chapter 1 \ One \#  for a chapter 
:::
:::showh2
1.1 \ Two \#\# for a section 
:::
:::showh3
1.1.1 \ Three \#\#\# for a subsection 
:::
:::

More details about chapter and section headings is in Section \@ref(sec:chapters).^[If you look at the source code for this section you'll see that the code to display the headings example doesn't match the markdown. This was a hacky way to get it to display correctly without messing up the section numbering If you find a solution for how to reset section numbers please let me know, because I've spent far too long on Stack Overflow today.]


### Text formatting {#sec:text-formatting}

You can make text *italic* using one \* or \_ at each end of the text. Bold uses two of each. 
```markdown
make italics using _one underscore_ or *one star* at each end
make bold using __two underscores__ or **two stars** at each end
```
:::showoutput
make italics using _one underscore_ or *one star* at each end

make bold using __two underscores__ or **two stars** at each end
:::

### Lists {#sec:lists}

Making lists is easy too!

```markdown
*   Bulleted lists
*   are made by putting
*   a \* (asterix) 
-   or a dash (-)
-   at the start of the line.
-   You don't even need to be consistent.
    - You can make a sub-list by adding 4 spaces before the -
        - but I'm not sure how many levels this goes to...
```
:::showoutput
*   Bulleted lists
*   are made by putting
*   a \* (asterix) 
-   or a dash (-)
-   at the start of the line.
-   You don't even need to be consistent.
    - You can make a sub-list by adding 4 spaces before the -
        - but I'm not sure how many levels this goes to...
:::

```markdown
1. To make a numbered list
2. you just number things
3. i.e. 1., 2., at the start of a line
7. and it doesn't matter
4. if your numbers are in the right order
1. or if you keep using
1. the same number
```
:::showoutput
1. To make a numbered list
2. you just number things
3. i.e. 1., 2., at the start of a line
7. and it doesn't matter
4. if your numbers are in the right order
1. or if you keep using
1. the same number
:::

You can also do
```markdown
a) Alphabetical lists
a) using lower case letters,
A) or using
A) upper case letters
```
:::showoutput
a) Alphabetical lists
a) using lower case letters,
A) or using
A) upper case letters
:::

or you can
```markdown
1. mix all of them together
    a) if that's how
        - you like to spend
        - your time. 
```
:::showoutput
1. mix all of them together
    a) if that's how
        - you like to spend
        - your time. 
:::


<!--chapter:end:intro-markdown.Rmd-->

# Chapters, sections, etc. {#sec:chapters}

## File Structure {#sec:file-structure}

Bookdown organises your notes into a book. The main page you see when you open a bookdown page is the `index.Rmd` file. 

Each chapter in the book has it's own markdown file. There are two options for defining the order of your chapters: using numbers in the file name, or specifying the order in the `_bookdown.yml` file.

### Specifying the order in `_bookdown.yml` {#sec:specify-order}

The easiest way to organise chapters is to specify the order in the `_bookdown.yml` file. For this method you don't need to have the numbers in the file name, and it's much easier to rearrange things. To specify the order, add an `rmd_files` section to the `_bookdown.yml` file and put the file names in the order you want them to appear:

```markdown
rmd_files:
  - index.Rmd
  - 01-intro.Rmd
  - 02-latex-conversion.Rmd
  - intro-markdown.Rmd
  - 03-chapters.Rmd
  ...
  - 99-references.Rmd
```

You'll see here that the `intro-markdown.Rmd` chapter doesn't have a number at the start. You don't need them for this method; bookdown will ignore the numbers and render things in your listed order. More details (that you probably won't need) about how this works are [here](https://bookdown.org/yihui/bookdown/usage.html). **Your references chapter should be the last one, otherwise it doesn't work properly.**

### Order by filename {#sec:order-filename}

This is the old method of ordering chapters. If you start from the bookdown template (described in Section \@ref(getting-started)), you'll see that the template files all start with a number. Bookdown will put the chapters in numerical order. For example, the following file names would order the chapters as they are in this book:

```markdown
index.Rmd
01-intro.Rmd
02-latex-conversion.Rmd
03-intro-markdown.Rmd
04-chapters.Rmd
... etc
```

I find this works reasonably well if you know beforehand what order you're going to put things in, but it quickly gets annoying if you decide later on that you want to add a chapter between two existing ones (you'd have to rename all the subsequent chapters), so I recommend using the method in Section \@ref(sec:specify-order). 

## Section labels {#sec:sec-labels}

The chapter title and label are given in the first line of the file.

```markdown
# Chapters, sections, etc. {#sec:chapters}
```

The `#` at the start of the line denotes a chapter heading (see Section \@ref(sec:heading-syntax)). The label is given in `{#sec:chapters}`. I think bookdown can create its own labels from the chapter/section heading, but it's easier to cross-reference if you set them yourself. 

You can make sections using two # at the start of the line:

```markdown
## Section labels {#sec:sec-lables}
```
Note that the `{#sec:label}` syntax is still the same - you don't need to say whether it's a chapter, section, subsection etc.

Subsections are done similarly, with three # at the start of the line.

You can change the section heading from "Chapter" to e.g. "Section" by editing the following in the  `_bookdown.yml` file:

```markdown
language:
  ui:
    chapter_name: "Section "
```

## Cross referencing {#sec:cross-ref}

You can reference other chapters, sections etc. throughout the book with the following syntax:

```markdown
Section \@ref(sec:cross-ref) is this section.
```

:::showoutput
Section \@ref(sec:cross-ref) is this section.
:::
When cross-referencing, you don't need the # before `sec`. That's only used to define the label.



<!--chapter:end:03-chapters.Rmd-->

# Figures {#sec:figures}

Adding figures {#sec:add-figs}
----------------

Figures and captions can be included, but the syntax is quite different to LaTeX.

Assuming that you would add a figure to a LaTeX document with the following code:

```
\begin{figure}
\begin{centering}
\includegraphics[width=0.7\textwidth]{Images/ho-tension.png}
\caption{This is a figure caption.}
\label{fig:ho-plot}
\end{centering}
\end{figure}
```

To include the figure in markdown the syntax is as follows:



````r
```
{r echo=FALSE, ho-plot, out.width='70%', fig.show='hold', fig.cap="This is a figure caption."}
knitr::include_graphics("Images/ho-tension.png")
```
````


\begin{figure}
\includegraphics[width=0.7\linewidth]{Images/ho-tension} \caption{This is a figure caption.}(\#fig:ho-plot)
\end{figure}

Important parts of this command:

 * `echo=FALSE` prevents the code being used to display the figure being shown.
 * `ho-plot` is your figure label. 
 * `out-width` is the output width of the figure.
 * `fig.cap` is the figure caption. 
 * `knitr::include_graphics` gives the location of the image file.
 
Figure captions {#sec:fig-captions}
------
Figure captions are controlled by the `fig.cap` setting. Your caption should be inside the double quotes. Remember to use \\" to escape any " characters you may use inside your caption. 

You can include cross-references and citations inside figure captions. See Sections \@ref(sec:fig-nos), \@ref(sec:cross-ref-eqs), and \@ref(sec:tab-syntax) for how to label and cross-reference figures, equations, and tables. Citations are covered in Section \@ref(sec:add-cite).

 
Figure numbering and referencing {#sec:fig-nos}
---------------

 Figures with labels and captions are numbered according to the section (e.g. Fig \@ref(fig:ho-plot) in this case). 
 
 To reference a figure use the syntax
 
 ```markdown
 \@ref(fig:label)
 ```
 where `fig:label` would be `fig:ho-plot` in this case.
 
 You can cross reference between sections/chapters, so make sure to use unique figure labels.

<!--chapter:end:04-figures.Rmd-->

# Equations {#sec:equations}



Equation syntax {#sec:add-eqs}
----------------

The syntax for equations is similar (but not identical) to LaTeX.

LaTeX code:

```markdown
\begin{equation}
\label{eqn:friedman}
	\left(\dfrac{\dot{a}}{a}\right)^2 + \dfrac{kc^2}{a^2} = \dfrac{8\pi G}{3}\rho 
\end{equation}
```

Rmd code:
```markdown
\begin{equation}
    \left(\dfrac{\dot{a}}{a}\right)^2 + \dfrac{kc^2}{a^2} = \dfrac{8\pi G}{3}\rho
(\#eq:friedman)
\end{equation}
```

\begin{equation}
    \left(\dfrac{\dot{a}}{a}\right)^2 + \dfrac{kc^2}{a^2} = \dfrac{8\pi G}{3}\rho
(\#eq:friedman)
\end{equation}

You can also use the latex
```markdown
\begin{align}
...
\end{align}
```
format for equations. If you're converting from LaTeX to markdown with pandoc it may convert equations to 
```markdown
\begin{aligned}
...
\end{aligned}
```
which also works.

:::fyi 
**LaTeX subequations and intertext**

I haven't been able to get subequations and intertext to work in bookdown. LaTeX equations of the form
```markdown
\begin{subequations}\begin{align}
\vec{E} &= \left( x,t \right)
\intertext{and in 3 dimensional space as}
\vec{E} &= \left( x,y,z,t \right)
\end{align}\end{subequations}
```

should be written as separate equations with the text between written outside the equation environment, e.g. 
```markdown
\begin{align}
\vec{E} &= \left( x,t \right)
\end{align}
and in 3 dimensional space as
\begin{align}
\vec{E} &= \left( x,y,z,t \right)
\end{align}
```
:::

Equation numbers and labels {#sec:eq-nos-labels}
------

The syntax for the maths is the same, but the labelling changes. To label and equation add

```markdown
(\#eq:label)
```
just before `end{equation}`. Only equations with labels will be numbered. If you don't want numbers then don't label the equations, but numbers are helpful.


Cross referencing equations {#sec:cross-ref-eqs}
-----

The syntax for cross-referencing equations is similar to sections and figures, i.e.

```markdown
Eqn. \@ref(eq:friedman) is the Friedman equation
```
will give "Eqn. \@ref(eq:friedman) is the Friedman equation".

Maths in captions {#sec:maths-captions}
------
R markdown gets a bit finicky about maths/symbols in captions. You may need to use two backslashes to escape symbols in figure captions. 

Example of a finicky caption:

`````markdown
```
{r echo=FALSE, gaussian, out.width='100%', fig.show='hold', fig.cap="Gaussian uncertainties. 
If we measure a value $x$ for a variable that has a true value $\\langle x \\rangle$ and
uncertainty $\\sigma$, there is a 68.3% probability that $x$ will be within 
$\\langle x \\rangle \\pm 1\\sigma$. There's a 95.4% probability of $x$ being within 
$\\langle x \\rangle \\pm 2\\sigma$, and 99.7% of $x$ being within  
$\\langle x \\rangle \\pm 3\\sigma$."}
knitr::include_graphics("Images/normal-curve.png")
```
`````

\begin{figure}
\includegraphics[width=1\linewidth]{Images/normal-curve} \caption{Gaussian uncertainties. If we measure a value $x$ for a variable that has a true value $\langle x \rangle$ and uncertainty $\sigma$, there is a 68.3% probability that $x$ will be within $\langle x \rangle \pm 1\sigma$. There's a 95.4% probability of $x$ being within $\langle x \rangle \pm 2\sigma$, and 99.7% of $x$ being within  $\langle x \rangle \pm 3\sigma$.}(\#fig:gaussian)
\end{figure}

Random tips {#sec:random-tips}
---------

<!-- **Angstrom symbol:** Use `$\unicode{x0212B}$` rather than `$\\AA$` to get $\unicode{x0212B}$. You can add `\\newcommand{\AA}{\unicode{x0212B}}` to index.Rmd file before the main text starts to make `$\AA$` work. -->

Does the $\si{\angstrom}$ symbol work now?

<!--chapter:end:05-equations.Rmd-->

# Tables {#sec:tables}

Markdown table generator {#sec:md-tab-gen}
------

If you're making a new table from scratch then the [Markdown Table Generator] (https://www.tablesgenerator.com/markdown_tables) is a handy tool.

Table syntax {#sec:tab-syntax}
----------------

Tables are a bit more simple in markdown than in latex. I haven't played around with complex tables much, but it's straightforward to make a basic one.

Example table:

```markdown
Table: (\#tab:planck-model) Base $\Lambda$CDM cosmological parameters from @Planck18. 

| Parameter | Best fit value     | Uncertainty |
|:--------------------|----------:|----------:|
| $\Omega_{b}h^2$ | 0.02233 | 0.00015 |
| $\Omega_{c}h^2$ | 0.1198 | 0.0012 |
| $\Omega_{m}h^2$ | 0.1428 | 0.0011 |
| $H_0$ | 67.37 | 0.54 |
| $\Omega_{m}$ | 0.3147 | 0.0074 |
| Age (Gyr) | 13.801 | 0.024 | 
| $z_{re}$ | 7.64 |  0.74 | 
| 100$\theta_{*}$ | 1.04108 | 0.00031 | 
```

Important things about tables:
 
 * Captions go **at the top** and should include the label definition.
 * Label syntax is `Table: (\#tab:table-name)`
 * Columns are delimited by `|`
 * Justification given by the colons and dashes in the line under the header row.
 
 Table: (\#tab:planck-model) Base $\Lambda$CDM cosmological parameters from @Planck18. 

| Parameter | Best fit value     | Uncertainty |
|:--------------------|----------:|----------:|
| $\Omega_{b}h^2$ | 0.02233 | 0.00015 |
| $\Omega_{c}h^2$ | 0.1198 | 0.0012 |
| $\Omega_{m}h^2$ | 0.1428 | 0.0011 |
| $H_0$ | 67.37 | 0.54 |
| $\Omega_{m}$ | 0.3147 | 0.0074 |
| Age (Gyr) | 13.801 | 0.024 | 
| $z_{re}$ | 7.64 |  0.74 | 
| 100$\theta_{*}$ | 1.04108 | 0.00031 | 

## Fancy tables {#sec:fancy-tables}

The `kableExtra` package is useful if you want to make fancier tables with things like grouped columns. The documentation is [here](https://haozhu233.github.io/kableExtra/bookdown/index.html) (it's a bit sparse...) but I'm including an example here to save you the time googling.



```r
library(kableExtra)
## read the table in from a file
df <- read.csv("lj_table.csv", header = TRUE, row.names = "Element")
## changing the names of my column headings. R starts
## counting at 1
names(df)[1] <- paste("$10^{-21}$ J")
names(df)[2] <- paste("meV")
names(df)[3] <- paste("$10^{-10}$ m")
## fancy formatting
df %>%
    kbl(caption = "Lennerd-Jones potentials, apparently. Shamelessly stolen from Sloan's lecture notes.",
        align = c("l", "c", "c", "c")) %>%
    ## only a little table, so stopping it being the full
    ## width of the page
kable_styling(full_width = F) %>%
    ## Grouping some columns
add_header_above(c(Element = 1, `$\\epsilon$` = 2, `$\\sigma$` = 1,
    `$r_0$` = 1), align = c("l", "c", "c", "c"))
```

\begin{table}

\caption{(\#tab:unnamed-chunk-5)Lennerd-Jones potentials, apparently. Shamelessly stolen from Sloan's lecture notes.}
\centering
\begin{tabular}[t]{l|l|c|c|c}
\hline
\multicolumn{1}{l|}{Element} & \multicolumn{2}{c|}{\$\textbackslash{}epsilon\$} & \multicolumn{1}{c|}{\$\textbackslash{}sigma\$} & \multicolumn{1}{c}{\$r\_0\$} \\
\cline{1-1} \cline{2-3} \cline{4-4} \cline{5-5}
  & \$10\textasciicircum{}\{-21\}\$ J & meV & \$10\textasciicircum{}\{-10\}\$ m & Å\\
\hline
He & 0.14 & 0.9 & 2.56 & 3.12\\
\hline
Ne & 0.49 & 3.1 & 2.75 & 3.36\\
\hline
Ar & 1.70 & 10.6 & 3.40 & 4.15\\
\hline
\end{tabular}
\end{table}

To get this to show your table, include the code above inside an `r` code chunk. The `echo=FALSE` part hides the code but still displays the result (which is probably what you want).


````r
```
{r echo=FALSE}

## table code goes here

```
````



<!--chapter:end:06-tables.Rmd-->

# Referencing {#sec:referencing}

## Adding citations {#sec:add-cite}

You can reference publications etc using the following syntax:

```markdown
@bibcode 
```
where `bibcode` is the bibcode for the entry in your bib file (see Sec. \@ref(sec:bib-files)). This will give the equivalent of the LaTeX `\citet{}` command.
Example:
```markdown
@2016Scowcroft showed that the SMC is a very elongated galaxy.
```
:::showoutput
@2016Scowcroft showed that the SMC is a very elongated galaxy.
:::
For `\citep{}`, just put the citation in square brackets.

```markdown
The first observations of SMC Cepheids were taken over 100 years ago [@1912Leavitt].
```
:::showoutput
The first observations of SMC Cepheids were taken over 100 years ago [@1912Leavitt].
:::

## Bibliography files {#sec:bib-files}

Assuming you're using natbib style references you can pass the same bib file to bookdown. 

Example bib file entry:
```markdown
@ARTICLE{1912Leavitt,
       author = {{Leavitt}, Henrietta S. and {Pickering}, Edward C.},
        title = "{Periods of 25 Variable Stars in the Small Magellanic Cloud.}",
      journal = {Harvard College Observatory Circular},
         year = "1912",
       volume = {173},
        pages = {1},
}
```

## Reference style {#sec:ref-style}

The referencing style is controlled in the `index.Rmd` file. 

At the top of this file you'll find the section that defines things like the book title, output format etc. The following lines are where you define the bib file names and referencing format. You can have multiple bib files.

```markdown
bibliography: [book.bib, packages.bib]
csl: apj.csl
link-citations: yes
```

The `bibliography:` line points to your bib file. The `csl:` line points to your style file. This book uses the ApJ style. Additional style files (e.g. PRL etc.) can be downloaded from [zotero](https://www.zotero.org/styles). `link-citations: yes` creates links from the citations in the text to the reference list at the end of the chapter. 

Any chapter that includes references will have a the reference list at the end. You can also include a separate reference list for the whole document. To make sure this goes right at the end I usually name my file `99-references.Rmd`. **If you specify the ordering as described in Section \@ref(sec:specify-order) you need to make sure that the reference chapter is the last one in the list.** The references file should contain the following text:


```r
`r if (knitr::is_html_output()) '# References {-}'`
```
This will create an **un-numbered** section at the end of the book called 'References' that has the full reference list.






<!--chapter:end:07-referencing.Rmd-->

# Publishing {#sec:publishing}

## Compiling the book {#sec:create-book}

To compile the book, press the 'Build Book' button in R Studio. You may need to press the arrow on the button and select `bookdown::gitbook` the first time you do this.

\begin{figure}
\includegraphics[width=0.5\linewidth]{Images/build-book} \caption{Build book button}(\#fig:build-book)
\end{figure}

This compiles all the markdown to create html files for the book. The output files should be in the `_book` folder.

## Publishing with github pages {#sec:gh-pages}

I use [github pages](pages.github.com) to publish my notes. 

The following assumes you are already using github to store your files. **TODO: Can add instructions for this part if needed**.

Once you have your book on github, create a new branch called `gh-pages`. This should enable the gh-pages setting in the repo.

Now clone the gh-pages branch into a directory called `book-output`

```
git clone -b gh-pages $repo-path book-output
```
where `$repo-path` is the location of the repository on github, e.g. `https://github.com/your_user_name/lecture_notes.git`

Then you want to copy all the output files into this directory and push it to the gh-pages branch.

```
cd book-output
cp -r ../_book/* ./
git add --all *
git commit -m "Update the book" || true
git push -q origin gh-pages
```

Your pretty lecture notes should now be online! You should find them at github_username.github.io/repo_name - or check the setting page of the repository. The address will be given in the github pages section.

## Publishing to Moodle {#sec:moodle-pub}

To be written soon....







<!--chapter:end:08-publishing.Rmd-->

# Things I should probably add to this guide at some point {#sec:todo .unnumbered}

## HTML output {.unnumbered .unlisted}
* editing css
  - what files?
  - where?
  - how?

## PDF output {.unnumbered .unlisted}
* latex stuff?
  - latex templates?
  - is this even a good idea?

## Adding new styles {.unnumbered .unlisted}
* adding `div` things to the css
* other stuff?

## Publishing to mooodle {.unnumbered .unlisted}
* quick way to update individual files. 

Suggestions for things that should be added are welcome. 

<!--chapter:end:inprogress.Rmd-->



<!--chapter:end:99-references.Rmd-->

