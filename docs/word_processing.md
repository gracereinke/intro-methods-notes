
# Typesetting and Word Processing Programs


## LaTeX

[LaTeX](https://en.wikipedia.org/wiki/LaTeX) is a document markup language (think something like HTML) that is widely used in academia.[^pronunciation]
Its primary advantages over Word (and word processors) are the separation of content and presentation and its formatting of mathematical equations.
In addition to papers, it is often used for academic slides; many talk slides are prepared with [beamer](https://en.wikipedia.org/wiki/Beamer_(LaTeX)).

[^pronunciation]: TeX is pronounced as "teck" because the X is a Greek chi. The pronunciation of
                  of LaTeX is thus lah-teck or lay-teck. It is not pronounced like the rubber compund.
                  See this [StackExchange](http://tex.stackexchange.com/questions/17502/what-is-the-correct-pronunciation-of-tex-and-latex) question on the pronunciation of LaTeX.

### Learning LaTeX

Here are some links to get started learning LaTeX:

- [Overleaf Free & Interactive Online Introduction to LaTeX](https://www.overleaf.com/latex/learn/free-online-introduction-to-latex-part-1)
- [LaTeX Tutorial](https://www.latex-tutorial.com/tutorials/) has interactive lessons
- [ShareLaTeX Documentation](https://www.sharelatex.com/learn/)
- [Overleaf Example Templates](https://www.overleaf.com/latex/templates/) has many different examples of LaTeX documents.
- [LaTeX Wikibook](https://en.wikibooks.org/wiki/LaTeX)
- [Not So Short Introduction to LaTeX](https://tobi.oetiker.ch/lshort/lshort.pdf) is a classic, but not as as newby friendly as the others.

### Using LaTeX

- Use an online service such as [Overleaf](https://www.overleaf.com/) or [ShareTeX](https://www.sharelatex.com/). These are great for collaboration, but become inflexible
  when you want to customize your workflow.
- Write it with a specialized editor such as [TeXmaker](http://www.xm1math.net/texmaker/), [TeXStudio](http://www.texstudio.org/), or [TeXshop](http://pages.uoregon.edu/koch/texshop/). These generally have
  built ways to insert text, and also live preview. I would stay away from editors such as [LyX](https://www.lyx.org/) that are WYSWYG.
- Write it with an general purpose editor such as [Atom](https://atom.io/) or [Sublime Text](https://www.sublimetext.com/).[^1] Most editors have a plugin
  to make writing LaTeX easier. For Atom there is [LaTeXTools](https://atom.io/packages/latextools), and for Sublime Text, [LaTeXTools](https://github.com/SublimeText/LaTeXTools)

[^1]: And of course [Vim](http://www.vim.org/) or [Emacs](https://www.gnu.org/software/emacs/).

### LaTeX with R

This is pretty easy. Rnw, also called Sweave, documents allow you to mix R chunks with LaTeX.
This is similar to R markdown, but with LaTeX instead of markdown.[^2]
[knitr](http://yihui.name/knitr/).

Many packages, such as xtable](https://cran.r-project.org/package=xtable), [stargazer](ttps://cran.r-project.org/package=stargazer), or [texreg](ttps://cran.r-project.org/package=texreg)  produce formatted output in LaTeX.
When you use these programs, do not copy and paste the output. Instead, save it to a file,
and use `\input{}` to include the contents in your document.

[^2]: And [Sweave](https://www.statistik.lmu.de/~leisch/Sweave/) files preceded R markdown and knitr by many years.


### My workflow

At the moment, I use emacs with [AucTeX](http://www.gnu.org/software/auctex/) for writing pure LaTeX documents.
However, I've started using Atom more and more as a general text editor.
I use RStudio for writing Rnw files.
For papers, I haven't moved to using (R) markdown and pandoc yet, because I find the layout and reference support still too
limiting; I end up writing enough LaTeX that I figure I might as well write the whole thing in LaTeX.
I've been using Overleaf for coauthoring in a couple of cases.
I often build my document using [Makefiles](https://en.wikipedia.org/wiki/Make_(software)).


## Word


While I use LaTeX in my own work, Microsoft Word is powerful piece of software,
and many of the complaints against Word come down to not being aware of its
features. There are many tools you can use to build your research paper;
whatever tool you use, learn how to use it proficiently.

### General Advice

This guide on using [Microsoft Word for Dissertations](http://guides.lib.umich.edu/c.php?g=283073&p=1886001)
covers everything and more that I would have. Also see [this](http://www3.nd.edu/~shill2/dtclass/word_2013_word_for_research_projects.pdf)

- [separate presentation and content](https://en.wikipedia.org/wiki/Separation_of_presentation_and_content) using styles
- Automatically number figures and tables
- Use a reference manager like [Mendeley](https://www.mendeley.com/), [Zotero](https://www.zotero.org/), [Colwiz](https://www.colwiz.com/app), or [Papers](http://www.papersapp.com/). They have plugins for citations in Word.
- When exporting figures for Word, if you must use a [raster graphic](https://en.wikipedia.org/wiki/Raster_graphics) use PNG files (not JPEG). For publication, use a high DPI (600) with PNG graphics.
- Learn to use *Fields*. You can insert figures from files that you can update using `Insert > Field > Links and References > IncludePicture`. This is useful for programmatically generating figures to insert into your document. Likewise,  you can insert text from files that you can update using `Insert > Field > Links and References > IncludeText`.

### Using R with Word

For a dynamic reports you can use [R Markdown](http://rmarkdown.rstudio.com/word_document_format.html) and export to a word document. When doing this, use a reference document to set the the styles that you will use.
See [Happy collaboration with Rmd to docx](http://rmarkdown.rstudio.com/articles_docx.html) for more advice on using R Markdown with Word.

When using functions from packages such as [xtable](https://cran.r-project.org/package=xtable), [stargazer](ttps://cran.r-project.org/package=stargazer), or [texreg](ttps://cran.r-project.org/package=texreg) output  HTML, which can be copy and pasted into word.

Finally, the [ReporteR][Create Word documents from R](http://davidgohel.github.io/ReporteRs/word.html) package is an alternative method to generate Word Documents from R.
