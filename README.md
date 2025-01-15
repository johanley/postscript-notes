# Postscript Notes 
Notes on using the [PostScript programming language](https://en.wikipedia.org/wiki/PostScript) (PS).

## General

* Adobe was founded in order to create PS in the early 1980s
* it sparked the *desktop publishing* idea, making typesetting much cheaper 
* PS is a full-blown computing language, but very much specialized on the task of telling printers how to print a document 
* it's all about **drawing**: both text and illustrations are executed by drawing lines, curves, and circles (vector, not raster) 
* it's all about **scalability**: it's trivial to rescale a document to a different size. (For changes to the aspect ratio, see below.)
* PS is the basis for [PDF](https://en.wikipedia.org/wiki/PDF) and [EPS (encapsulated PostScript)](https://en.wikipedia.org/wiki/Encapsulated_PostScript)
* it's all about **print quality**: for printing outputs of the highest quality, printing houses often request PDF/EPS/PS file formats
* it can be argued that, in a sense, PS is **at the center of the print universe**
* programming in PS isn't getting much attention from programmers these days
* PS gives you some insight into two worlds: graphic operations (transforming coordinate systems, graphics stack), and printing details (CMYK, color profiles) 

## Lingo

* PLRM - PostScript Language Reference Manual (link below)
* [Document Structuring Conventions (DSC)](https://en.wikipedia.org/wiki/Document_Structuring_Conventions) - a standard way of laying out PS files.
* **distilling** a PS document means changing it from code+data to just data only, roughly speaking. A PS file is "distilled" into a PDF file.  
* the built-in **operators** are the core of the language. There are quite a few of them, but 80% of the time you use 20% of the operators.
* **page independence** is the idea that each page should be independent of all other pages. This is achieved by saving the state of all items in memory 
at the start of a page, and then restoring that state when the page has been completed.
* **prolog**: the start of a PS file, containing <em>procedures</em> that help build pages. The prolog is usually written "by hand".
* **script**: placed after the prolog, a script builds pages by passing data to the procedures defined previously in the prolog.
The script is typically created programmatically.
* **driver**: the program that creates the final output PS file, usually by dynamically building the script 

## Standard References from Adobe
* The Red Book: The PostScript Language Reference Manual (PLRM). <b>This is a must-have for programmers.</b> 
PS has three <em>Language Levels</em>. The three editions of this book correspond to the three levels. 
The [third edition (1999)](https://www.adobe.com/jp/print/postscript/pdfs/PLRM.pdf) is the most up to date. 
The [second edition (1990)](https://archive.org/details/postscriptlangua0000taft) is useful because it includes an appendix for the Document Structuring Conventions (DSC).
* The [Blue Book](https://archive.org/details/postscriptlangua0000unse): Tutorial and Cookbook (1986)
* The [Green Book](https://archive.org/details/postscriptlangua00reid): Program Design (1988)

Also of note: 
* [Mathematical Illustrations](https://personal.math.ubc.ca/~cass/graphics/manual/) by Bill Casselman, a mathematician at UBC.
* [Learning PostScript by Doing](https://staff.science.uva.nl/a.j.p.heck/Courses/Mastercourse2005/tutorial.pdf) by André Heck, University of Amsterdam (2005).


## Tools
* [Ghostscript](https://www.ghostscript.com/) for creating, viewing, and converting PS files, and related tasks. **Very commonly used when developing with PS.**
* Adobe Distiller in Adobe Acrobat converts PS files to PDF files. (I haven't used this yet, so I don't know much about it.)


## PS as a Programming Language
* a special-purpose language for describing documents
* plain text files; interpreted, not compiled
* minimalist syntax
* **code-as-data** is central to the language: anonymous procedures are passed around as data
* types: boolean, integer, real, name 
* data structures: strings, dictionaries, and arrays (nestable)
* strings can contain binary data! **there's no 'character' data type as such**  
* strings are arrays of numbers in the range 0..255
* a procedure is an executable array of strings (tokens in the language) 
* it has next to nothing for implementing modularity (the `run` operator includes one PS file in another)
* Language Level 3 has 385 operators  
* it makes liberal use of **stacks and dictionaries** 
* it uses an **operand stack** to both pass parameters and return results
* there's no strict concept of a reserved word: the behaviour of built-in operators can be overridden by a program!
* the default origin of the coordinate system on the page is the lower left corner. The x coord increases to the right, and the y coord increases upwards.
* PS has three Language Levels 1-3; I assume most modern implementations probably implement Level 3, since it's been around for a long time.
* the documentation for the language reflects the lessened capabilities of typical computers dating from the 1980s and 1990s. 
I assume that those concerns have become obsolete.

<em>"[PostScript] includes the ability to treat programs as data and to monitor and control many aspects of the language's execution state; these notions
are derived from programming languages such as LISP."</em> (Red Book, page 23).

<em>"[The] names that represent operators are not reserved by the language. A PostScript program may change the meanings of operator names."</em>
(Red Book, page 23). 

<P>The generic name for everything in the language is an *object* (not in the sense of object-oriented programming!).

The interpreter executes a series of objects. An object has:
* a type: boolean, integer, real, name, operator (*simple*); string, array,dictionary, file (*composite*)
* attributes: literal or executable; access control (read, execute, both)
* a value
 
If an object is executable, executing the object depends on its type:
* for a number: pushes a copy of the number onto the operand stack
* for a name: look up the name in a dictionary, fetch the related value, and execute it
* for an operator: do a built-in action (add numbers, paint characters, etc.)

**A procedure is an array of executable objects.**

Syntax:
* `(blah)`  - a string 
* `[1 2 (hello)]` - an array
* `<<(A) 12.25 /B 3.14 >>` - a dictionary (map); the order is key-value, key-value ... Keys are usually names. 
I've seen the system silently coerce string-keys to name-keys!
* `/blah` - a **name** literal - names of data (variables, procedures); with a slash means literal, without the slash means executable
* `{...}` - a **procedure** (an executable array)
* `5 3 sub` - **the params come first!** This is '5 - 3 = 2'. 5 and 3 are placed on the stack. The 'sub' operator is executed. It consumes two 
items from the stack, and outputs the result 2 to the stack.

From the Red Book (page 23):
**"A string is similar to an array, but its elements must be integers in the range 0 to 255."** 

**"String objects are conventionally used to hold text, one character per string element. However, the PostScript language does not have
a distinct "character" syntax or data type and does not require that the integer elements of a string encode any particular character set. 
String objects may also be used to hold arbitrary binary data.**""

*"To enhance program portability, strings appearing literally as part of a PostScript program should be limited to characters from the 
printable ASCII character set, with other characters inserted by means of the \ddd escape conventions..."*
(I'm successfully ignoring this advice when injecting data into a PostScript template. I don't think it's necessary for my purposes, at least.) 

**PostScript predates Unicode.** See below for an important remark about encoding.

Dictionaries: 
* are central to the language
* are where data is stored
* there's always a <em>current dictionary</em>
* there's a **stack of dictionaries**: `systemdict` (the lowest, for built-in operators; read-only), `globaldict`, `GlobalFontDirectory`, `userdict`, `FontDirectory`, ... 
* you often create your own dictionaries to go on top of userdict (local variable idiom)
* **searches by key-name proceed from the top of the dictionary stack to the bottom**
* **if you define something with the same name as a built-in operator, you will hide/override the built-in implementation**
* the operator `def` adds new data to a dictionary (it acts like a 'put') 


The Five Stacks:
* operand stack: data being passed in and out of procedures. *Often referred to simply as 'the stack'. Operations on the stack are important*.
* dictionary stack: where most of your data is stored: you create dictionaries on the top of this stack. Name searches are executed against it.
* graphics state stack: allows you to save-restore different graphic states
* clipping path stack
* execution stack: procedures and files being executed; the call stack. Programs don't talk to this directly.


**Stack operations are important because they are used so often.** 


## Working with PS as a Programmer  
* Adobe's documentation for the language is robust and authorative
* it's definitely low-level
* it's curt
* reading it often requires more concentration than is usual for a programming language
* I don't know of anything resembling an IDE for PS
* debugging PS is very rudimentary. Adobe has an <em>ehander.ps</em> which can give better stack traces etc., but I haven't used it yet.
* the conversion from PS to PDF for the final output is a non-issue, since it's handled by well-known and stable tools (Ghostscript and Adobe Acrobat/Distiller).
* there's no real community of users producing libraries for it
* crickets: there's very little discussion of PS amongst modern programmers. Given the ubiquity of PDF and EPS, this is rather strange.
* outputs in English and French that can use the 8859-1 (Latin-1) character encoding are straightforward (see below).
* outputs in Chinese, Japanese, Korean and so on are possible, but more complicated.

In Language Level 3, there are 385 operators listed in the manual.
Initially, this gives you the impression that PS is large and complex. 
But after using it for a few weeks, it seems that the opposite is the case: 80% of the time, you use 20% of the operators. 
As usual, it's not only about the language, but the language <em>plus the libraries</em>.
In a sense, PS is all language and few libraries, so you could argue that its overall size is small.

This makes sense: PS is a special purpose language, designed mainly for printing documents.
There are no libraries for date-time control, regular expressions, threads, and so on.

There are some common items that most programmers feel should have been part of the language.
There's no help in the core language operators for:
* string concatenation!
* text flow
* table creation 


## Producing Documents Programmatically With PS

The rock-bottom simplest case for using pure PS to generate documents is when:
* there are no large chunks of text, which need to have their text-flow managed
* the general emphasis is on data, in some sense, instead of large amounts of text
* you can use its drawing capabilities to make illustrations (graphs, charts, diagrams, and so on)

In PS, managing text becomes more complicated when: 
* the blocks of text are large, and can flow onto subsequent pages (widows, orphans, and so on)
* the blocks of text change fonts in mid-stream (because you have to interpret your own <em>ad hoc</em> markup mechanism)

Pure PS is entirely at the low end of the spectrum. 
At the other end would be tools like Adobe's [InDesign](https://www.adobe.com/ca/products/indesign.html).



## The 'Lifecycle' of a Glyph, and Encoding
In PS, the `show` operator marks a page with text characters.
Here, a simple literal is passed the operator:

```
(Hello) show
```
 
It's important to understand how the system marks the letter 'H' on the page:
* the system **passes a number to `show`**, not a character! There is no 'character' data type in PS. *In PS, 'string' means a string of numbers, all in the range 0..255.*
* the number comes *from the file's encoding of the character*. For English/French, the file encoding must be Latin-1 (8859-1).
* that number is passed to the `show` operator. PS calls this number the *character code*.
* the current font has all of its info in a font-dictionary. One entry in that dictionary defines how it maps an incoming 
number (the character code) to a glyph that it knows how to draw.
* that entry has `/Encoding` as its key, and its value is an array containing 256 names. This array is called the **encoding vector**.
* the names in the encoding vector are the names of glyphs supported by the font, such as `/Hsmall` and `/hyphen`
* the incoming character code is used is an index into the encoding vector
* a glyph name is returned
* it's used to look up the drawing instructions for that glyph
* the glyph is drawn at the `currentposition`
* when finished, the `currentposition` is updated by the font to be in the desired position for the next letter
* the update to the new position is a displacement dx and dy from the initial position that was originally passed
* each glyph can have a different displacement
* for English, dx&gt;0 and dy=0 (flow left to right)
* for Arabic or Hebrew  dx&lt;0 and dy=0 (flow right to left)
* for vertical Chinese, dx=0 and dy&lt;0 (flow top to bottom)
* for monospaced (fixed width) fonts, the displacement is the same for every glyph. Most fonts are not monospaced.

Many fonts support fewer than 256 glyphs.
In that case, the encoding vector will have entries of `.notdef`.
It can be repeated in many places in the encoding vector.
If the system passes in a character code that corresponds to a `.notdef` entry, then the font is required to define a special glyph to act as a placeholder.
This is the source of some weird characters that you sometimes see when the encoding is not correct in a system. 




## Re-encoding Fonts to Latin-1 (8859-1)
PS was designed before [Unicode](https://en.wikipedia.org/wiki/Unicode) was invented.
It's possible to use it to print documents in languages such as Chinese, with large character sets, but 
it's more complicated than creating a document using a Latin-1 (8859-1) encoding. (I've never done that, so I have no insight on that.) 

For documents in Western European languages like English and French, here are the steps that you need to do:
1. Your PS files need to be encoded using Latin-1 (8859-1).
2. Every time your application reads/writes a PS file, the Latin-1 encoding needs to be used.
3. You need to "re-encode" every font that you use, to use Latin-1.

The re-encoding of a font is necessary since the default encoding is an old Adobe encoding that nobody uses anymore.
The re-encoding code (below) copies a font's definition into a new place, with a new font-name, and retains all of the info except for its `Encoding` entry.
The `Encoding` entry is changed to Latin-1 (8859-1).

Here's a procedure that re-encodes an existing font:
```
% new-font-name old-font-name
/latinize {
  findfont
  dup length dict 
  begin
    % copy everything in the font's dict except for the FID (a unique id for a font):
    { 1 index /FID ne {def}{pop pop} ifelse } forall
    % override the encoding entry in this dict:
    /Encoding ISOLatin1Encoding def
    % push this font-dict onto the operand stack:
    currentdict  
  end
  definefont  % internally sets a FID (font-id) for the new font
  pop % remove the new font object from the operand stack
} bind def
```

After this is done, the new font, under the new name you have given it, is available like any other font in the system.



## Understanding the Path

<em>"In the PostScript language, **paths** define shapes, trajectories, and regions of all sorts.
Programs use paths to draw lines, define the shapes of filled areas, and specify boundaries for clipping other graphics."</em>

<em>"A path is made up of one or more disconnected **subpaths**, each comprising a sequence of connected segments."</em>

The idiom for creating a path is:
```
newpath
12 50 moveto
..more path construction here..
..`closepath` often comes at the end..
```

The endpoint of the current path is called the *current point*.

There are a number of operators that interact with the current path.
It helps to know the details of how such operators affect it.

*Creates* a new path:
* `newpath`
* `moveto` starts a new *subpath* at a new position, but doesn't add a line segment. It's like moving a pen above paper, without making a mark.

*Appends* to the current path :
* `lineto`, `curveto`, `arc` (and similar operators, such as `rlineto`, which uses relative displacements) 
* `closepath`   

*Consumes*  the current path (and clears it with an implicit `newpath`):
* `stroke`
* `fill` - this also implicitly calls `closepath` when needed!

Note: 
* having a `newpath` immediately after a `stroke` or `fill` is redundant.
* `stroke` paints the outline of a closed path, while `fill` paints the interior, without painting the outline.

*Copies* the current path:
* `clip` creates a clipping region using the current path, and leaves the current path in place



## The Drawing Pattern

* set up the coordinate system: `translate` (very common), `scale`, `rotate` (less common)
* create a path
* `stroke` and/or `fill`

The entire sequence is usually surrounded by `gsave` and `grestore`. 
This restores the graphic state back to its original condition.

You can both `stroke` and `fill` the same path by wrapping the first painting operation with its own `gsave`-`grestore` pair.  

If the same path is used more than once, consider defining the path in its own proc.


## Idiom: Local Variables
PS has no built-in scoping of variables to a procedure.
You can achieve something close to that by using a temporary dictionary for your data.
The pattern looks like this:

```
/my-wondrous-proc {
  10 dict begin
    ...
  end 
}
```

The first line has a `begin` operator that creates an anonymous dictionary and puts it on top of the dictionary stack. 
In this case, the dict is initialized to hold 10 items; but, if you accidentally use it to store more than 10 items, the system 
will automatically re-size the dict as needed. 
Each `begin` is paired with an `end` operator at the end of the proc.
This simplies removes/destroys the temporary dictionary.

This is not 100% local: if proc A calls a helper proc B, then proc B sees the exact same dictionary stack as proc A.
(In the language of object-oriented programming, the data behaves somewhat like an object's field, not a local variable defined in a method.)



## Idiom: Move Data on the Stack into a Dictionary
There is a trade-off between using data on the stack directly, versus pulling the data off the stack and storing it in a dictionary.
Among the more experienced, there may be a tendency to use the stack directly, perhaps rearranging the order of things using stack operators. 

For the following proc, say it has two arguments passed to it, called dx and dy.
This implementation defines two items in its temporary dictionary, to store the data under a name.
Note the use of the `exch` operator, which is used to switch the order of the top two things on the stack.
That switching is needed (unfortunately) because of how the `def` operator is defined.

```
% pass dx dy as arguments
/my-delightful-proc {
10 dict begin
  /dy exch def
  /dx exch def
  ...
end 
}
```


## Idiom: Save/Restore for Page Independence
Adobe recommends having pages independent of each other.
The typical way to accomplish this is to wrap the code that generates a page in a ``save\restore`` pair.
In this way, the state of memory is saved at the beginning of the page, and resurrected at the end, such that each page 
starts with (local) memory that is in the exact same state.

Here's an example. It includes some DSC comments (Document Structuring Conventions):

```
  %%Page: 1 1 
  %%BeginPageSetup 
   /pgsave save def
  %%EndPageSetup
    ...the page is drawn here..
  pgsave restore
  showpage
  %%PageTrailer  
```


## Idiom: Include Files
You can include one PS file in another by using the `run` operator.
This simply opens a given file and injects its content into the current position.

`run(some-code.ps)`

  
## Idiom: Get Clean Joins With closepath
When you construct a closed path, instead of closing it explicitly, you should usually 
close it with the `closepath` operator. 
The reason is that it makes a visually pleasing join by default (miter).


## Idiom: Use Whitespace to Increase Legibility
PS can be hard to read. 
You have to sometimes concentrate on the visualizing what operators are doing, and 
track the state of the operand stack in your head. 

Example:
```
%  the month appears in the top left corner of the cell
{0 cell-dy 75 pct translate                         
month-name cell-dx 2.5 pct cell-dy 10 pct moveto show} draw
```

If you add simple whitespace, to group together related items, then it's easier to read;
```
%  the month appears in the top left corner of the cell
{0   cell-dy 75 pct   translate                         
month-name   cell-dx 2.5 pct   cell-dy 10 pct   moveto show} draw
```



## Idiom: Use a Proc For Drawing
Drawing operations are usually of the form

```
gsave
..translate..
..draw..
grestore
```

It's possible to put that pattern into a proc, as a template:
```
% pass a procedure 
% draw something, and then have this proc restore the graphics state
% the first line of the passed proc is usually a 'translate' operation
/draw {
  gsave 
    newpath % ensures the currentpath/point is empty
    exec  % execute the passed proc
  grestore
} bind def

% the caller looks like this:
{.. .. translate
  ...
} draw 
```

Variation: you might consider putting the `translate` operator inside the draw proc itself.




## Idiom: List The Contents of a Dictionary

```
% for debugging only
% pass a dict; shows its keys and values on stdout
/list-dict {
 { == ==  } forall
} bind def
```




## Use CMYK for Print, RGB for Screens
Adobe has the idea of *color spaces*. 
You almost always use the CMYK color space for print outputs, and RGB for an output being viewed on a screen. 



 
## Consider Using Percent Coordinates
Probably the first decision in many projects is the choice of dimensions for the page.
If you code explicitly to those dimensions, then that one decision will be all over your code.
That one decision becomes more or less locked-in.

Instead of using absolute units, you should consider using percentage units instead.
In that case, the decision about the actual format can be implemented in a single line of code,
with all other coordinates being defined relative to some base as a percentage.

One of the main advantages of PostScript is that of scalability. 
It seems a shame to throw that away by locking in to a single format.
(Note that using the `scale` operator to change size only works if the aspect ratio remains the same.)   

In the web, [pages that are responsive](https://en.wikipedia.org/wiki/Responsive_web_design) to 
changes to screen size and orientation have become ubiquitous.
The exact same idea can be applied to print. 


## Margins That Are Sensitive to the Position of the Binding
Many, many books published these days have margins that are the same left-right.
This often creates annoying problems for the reader, if the text is close to the binding.
If your output is to be bound, consider making your margins asymmetric, with more margin near the binding.




