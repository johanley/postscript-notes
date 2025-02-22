# Postscript Notes 
Notes on using the [PostScript programming language](https://en.wikipedia.org/wiki/PostScript) (PS).

## General

* Adobe was founded in order to create PS in the early 1980s
* it sparked the *desktop publishing* idea, making typesetting much cheaper; it was the first tool to use scalable fonts 
* PS is a full-blown computing language, but very much specialized on the task of telling printers how to print a document 
* it's all about **drawing**: both text and illustrations are executed by drawing lines and curves (vector, not raster) 
* it's all about **scalability**: it's trivial to rescale a document to a different size. (For changes to the aspect ratio, however, see below.)
* PS is the basis for [PDF](https://en.wikipedia.org/wiki/PDF) and [EPS (encapsulated PostScript)](https://en.wikipedia.org/wiki/Encapsulated_PostScript)
* it's all about **print quality**: for printing outputs of the highest quality, printing houses often request PDF/EPS/PS file formats
* it can be argued that, in a sense, PS is **at the center of the print universe**
* PS gives you insight into graphic operations (transforming coordinate systems, graphics stack), how fonts work, and printing details (CMYK, color profiles)
* PS seems to have been a major source of inspiration for other languages. For example, in Javascript, the operations on the canvas object are very similar to 
operations found in PS. It's as if PS got it right, and many other tools have used it as a guide for how to treat graphics and fonts. 
* PS is intended in part for *machine generation*, in which you write code that writes PS code. There's a mix: some PS is handwritten, and some is 
generated by a program that emits PS code that calls the handwritten code, passing data to the procs you've written by hand.
* if your application needs to generate PDF, a viable option is to generate PS instead: the conversion from PS to PDF is never a problem (Ghostscript, Adobe Acrobat).
* PS doesn't seem to be getting much attention from programmers these days

## Lingo

* PLRM - PostScript Language Reference Manual (link below). This is Sacred Writ.
* [Document Structuring Conventions (DSC)](https://en.wikipedia.org/wiki/Document_Structuring_Conventions) - a standard way of laying out PS files.
* **distilling** a PS document means changing it from code+data to just data only, roughly speaking. A PS file is "distilled" into a PDF file. A PDF file is a 
static data structure, essentially a tree of dictionaries, not really meant for direct human consumption.  
* the built-in **operators** are the core of the language. There are about 385 of them in Language Level 3, but 80% of the time you use 20% of the operators.
* **page independence** is the idea that each page should be independent of all other pages. This is achieved by saving the state of memory (local VM)
at the start of a page, and then restoring that state when the page has been completed.
* **prolog**: the start of a PS file, containing <em>procedures</em> that help build pages. The prolog is usually written "by hand".
* **script**: placed after the prolog, a script builds pages by passing data to the procedures defined previously in the prolog.
The script is typically generated programmatically (by a *driver* application).
* **driver**: the program that creates the final output PS file, usually by dynamically building the script
* **translator**: a specialized tool that translates one page description language (PCL, DVI) into another (PostScript) 

## Standard References from Adobe
* The Red Book: The PostScript Language Reference Manual (PLRM). <b>This is a must-have for programmers. All of the answers are in here.</b> 
PS has three <em>Language Levels</em>. The three editions of this book correspond to the three levels. 
The [third edition (1999)](https://www.adobe.com/jp/print/postscript/pdfs/PLRM.pdf) is the most up to date. 
The [second edition (1990)](https://archive.org/details/postscriptlangua0000taft) is useful because it includes an appendices for the 
Document Structuring Conventions (DSC) and Encapsulated PostScript (EPS).

These secondary references are more dated than the Red Book. They have some useful ideas, but shouldn't be taken too seriously:
* The [Green Book](https://archive.org/details/postscriptlangua00reid): Program Design (1988) is a gentle intro to the language
* The [Blue Book](https://archive.org/details/postscriptlangua0000unse): Tutorial and Cookbook (1986)

Also of interest:
* [Thinking in PostScript](https://hint.userweb.mwn.de/compiler/ThinkingInPostScript.pdf) by Glenn Reid (1990). 
* [Taking Advantage of PostScript](https://www3.nd.edu/~jsherman/portfolio/PSforD/PSforD.pdf) by John Sherman, University of Notre Dame. 
* [Mathematical Illustrations](https://personal.math.ubc.ca/~cass/graphics/manual/) by Bill Casselman, a mathematician at UBC.
* [Learning PostScript by Doing](https://staff.science.uva.nl/a.j.p.heck/Courses/Mastercourse2005/tutorial.pdf) by André Heck, University of Amsterdam (2005).
* James Gosling, the creator of Java, also [worked on the NeWS windowing system](https://donhopkins.medium.com/alan-kay-on-should-web-browsers-have-stuck-to-being-document-viewers-and-a-discussion-of-news-5cb92c7b3445), which was based on PostScript.
* [L-Systems in PostScript](https://levelup.gitconnected.com/programming-l-systems-in-postscript-3959abdfba90) by Michel Charpentier.

## Tools
* [Ghostscript](https://www.ghostscript.com/) for creating, viewing, and converting PS files, and related tasks. **Very commonly used when developing with PS.**
* Adobe Distiller in Adobe Acrobat converts PS files to PDF files. (I haven't used this yet, so I don't know much about it.)


## PS as a Programming Language
* a special-purpose language for describing documents
* plain text files; interpreted, not compiled; garbage collection
* minimalist syntax
* no reserved words! You can re-define the behaviour of built-in operators
* **post-fix syntax** is used, with the params coming before the operator/procedure
* **code is data and data is code** is central to the language: anonymous procedures are passed around as data
* types: boolean, integer, real, name (their word for an identifier) 
* data structures: dictionaries, arrays, strings (nestable)
* strings can contain binary data! **there's no 'character' data type as such**  
* strings are arrays of numbers in the range 0..255 (a character's number is determined by an *encoding*)
* a procedure is an executable array of strings (tokens in the language) 
* it has a dynamic namespace, implemented by the dictionay stack
* it doesn't seem to have much for implementing modularity (the `run` operator includes one PS file in another)
* the programmer writes **procedures**, but the system has built-in **operators** (385 of them in Language Level 3). 
In your code, operators and procedures are used in the exact same way. 
* PS makes liberal use of **stacks and dictionaries** 
* PS uses an **operand stack** to pass parameters and return results
* PS **may have been the first language to implement dictionaries**, but I'm not sure. The documentation emphasizes in several places 
that dictionaries are a unique aspect of the language.
* the default origin of the coordinate system on the page is the lower left corner. By default, the x coord increases to the right, and the y coord increases upwards.
* PS has three Language Levels 1, 2, and 3. I assume most modern implementations probably implement Level 3, since it's been around for a long time.
* the documentation for the language reflects the lessened capabilities of typical computers dating from the mid 1980s and 1990s. 
I assume that those concerns have become obsolete.

<em>"[PostScript] includes the ability to treat programs as data and to monitor and control many aspects of the language's execution state; these notions
are derived from programming languages such as LISP."</em> (Red Book, page 23).

<em>"[The] names that represent operators are not reserved by the language. A PostScript program may change the meanings of operator names."</em>
(Red Book, page 23). 

<P>The generic name for everything in the language is an <em>object</em>, but this is not meant in the same sense as object-oriented programming.

The interpreter executes a series of objects. An object has:
* a type: boolean, integer, real, name, operator (*simple*); string, array,dictionary, file (*composite*)
* attributes: literal or executable; access control (read, execute, both); length (for composite objects)
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
PS will silently coerce string-keys to name-keys!
* `/blah` - a **name** literal - names of data (variables, procedures); with a slash means literal, without the slash means executable
* `{...}` - a **procedure** (an executable array)
* `5 3 sub` - **the params come first!** This is '5 - 3 = 2'. 5 and 3 are placed on the stack. The 'sub' operator is executed. It consumes two 
items from the stack, and outputs the result 2 to the stack.

From the Red Book (page 23):
**"A string is similar to an array, but its elements must be integers in the range 0 to 255."** 

**"String objects are conventionally used to hold text, one character per string element. However, the PostScript language does not have
a distinct "character" syntax or data type and does not require that the integer elements of a string encode any particular character set. 
String objects may also be used to hold arbitrary binary data.**"

*"To enhance program portability, strings appearing literally as part of a PostScript program should be limited to characters from the 
printable ASCII character set, with other characters inserted by means of the \ddd escape conventions..."*
(I'm successfully ignoring this advice when injecting data into a PostScript template. I don't think it's necessary for my purposes, at least.) 

**The first version of PostScript predates Unicode.** See below for an important remark about encoding. 
PostScript can handle any character set, but extended character sets (Chinese, Japanese, and so on) are more complex to use than small character sets.

*"PostScript fonts are executable programs that draw the character shapes using the same path construction and painting mechanisms as all other graphics."*

**Dictionaries**: 
* are central to the language
* are where data is stored
* there's a **stack of dictionaries** with three permanent members: `systemdict` (the lowest, for built-in operators), `globaldict`, and  `userdict`
* the <em>current dictionary</em> is simply the one on top of the stack
* you often create your own dictionaries to go on top of userdict; such dictionaries are usually short-lived, and provide "extra names" that exist only during the execution of a proc. 
* the **dictionary stack implements a dynamic namespace, against which names are looked up at a given line in your code. This is the most important thing about the dictionary stack.** 
* **searches by key-name proceed from the top of the dictionary stack to the bottom**. This allows you to easily build default-plus-override behaviour, if desired.
It also means that a name will shadow/hide any identical name appearing in a lower dictionary.
* **if you define something with the same name as a built-in operator, you will hide/override the built-in implementation**.
* the operator `def` adds new data to the current dictionary (it acts like a *put* - create or update)
* warning: strings `(blah)` and names `/blah` are interchangeable when used as keys in dictionaries. If you create an entry with a string as key, the 
dictionary will return that key as a name data-type! 


*"The interpreter maintains a dictionary stack defining the current dynamic name space."*

The dictionary stack, from top to bottom:
* (anonymous) - a temporary dictionary created in the context of a single proc; a temporary modification to the current namespace
* `userdict` - used by programs, local VM; permanent
* `globaldict` - used by some programs, global VM; permanent
* `systemdict` - read-only, built-in operators, global VM; permanent

Note how the order reflects what you might informally call the *life expectancy* of the data.
From bottom to top, the life expectancy of the data gets shorter and shorter, roughly speaking.  

In error messages, keep an eye on information about the state of the dictionary stack.
An example from Ghostscript:

<pre>
Dictionary stack:
   --dict:757/1123(ro)(G)--   --dict:0/20(G)--   --dict:89/200(L)--</pre>
   
This says that, at the time of this erorr, there were 3 dictionaries on the dictionary stack.
These three will always be present. 
They are, in order from left to right, `systemdict`, `globaldict`, and `userdict`.

For the `globaldict`, the above says:
* it has 757 entries, and has allocated room for 1123 entries 
* it's read-only (ro)
* it's in global VM (G)


**Names**:
 
*"A name is an atomic symbol uniquely defined by a sequence of characters. Names serve the same purpose as 'identifiers' in other
programming languages: as tags for variables, procedures, and so on. However, PostScript names are not just language artifacts, but are 
first class-data objects, similar to 'atoms' in LISP."*

"Names do not *have* values, unlike variable or procedure names in other programming languages. However, names can be *associated* with values in dictionaries."

By default, names are bound *late*: the system looks up the value (in the namespace/dictionary stack) associated with an executable name only at execution-time,
when a procedure is invoked. 
The `bind` operator changes this behaviour, but only for the *operators* in a proc (and on not other procs that the given proc will invoke). 
At the time the `bind` operator executes on a proc, it can modify its contents, by replacing operator names within it 
with the associated values *at the time the bind is executed*.
This also applies to all procs *called by* the given proc, to an arbitrary depth.

Benefits of `bind`: 
* it avoids possible changes to the definition of operators in the namespace, between the definiton of a proc and its execution.  
* it usually results in faster execution. 
* *"It is worthwhile to apply `bind` to any procedure that will be executed more than a few times."*

For names that aren't operators, the exact same behaviour is implemented by replacing the name `blah` with `//blah`.
Again, this doesn't immediately *execute* the name, it immediately *replaces* it with its value according to the current namespace (the dictionary stack). 



**Local VM and Global VM**:
* the distinction was added in Level 2 of the language
* global VM: stores things that exist for the full duration of your program
* local VM: a kind of workspace in which each page creates its output *independently of the other pages*
* a page wraps its code in a `save`-`restore` pair. The `save` operator saves the state of local VM. 
The `restore` operator restores the state of local VM back to what it was at the previous `save`, at the end of generating a page. 
In this way, each page starts with the same initial conditions, and has no effect upon other pages.
* the `save`-`restore` pair has no effect on global VM.
* global VM can be used to store, for example, procs and constants that should exist for the duration of the program, and 
be accessible to all pages.
* no global-to-local cross-talk: a composite object in global VM can't have its value in local VM (because local VM gets clobbered at the end of every page).
* it's dangerous to put mutable items in global VM (for the usual reasons)

"Typically, global VM should be used to hold procedure *definitions* and constant data; local VM should be used to hold temporary data needed 
during *execution* of the procedures."

"Only composite objects occupy VM. An 'object in VM' means a 'composite object whose *value* occupies VM'."

**The Five Stacks**:
* operand stack: data being passed in and out of procedures. *Often referred to simply as 'the stack'. Operations on the stack are important*.
* dictionary stack: where most of your data is stored: you create dictionaries on the top of this stack. Name searches are executed against it.
* graphics state stack: allows you to save-restore different graphic states
* clipping path stack
* execution stack: procedures and files being executed; the call stack. Programs don't talk to this directly.

*"Programming in the PostScript language is primarily an exercise in stack manipulation."*

**Stack operations are important because they are used so often.** 

Data exists only in these places:
* on the operand stack (where it has no name)
* somewhere in the dictionary stack (where it has a name, and is part of the current namespace)


## Working with PS as a Programmer  
* Adobe's documentation for the language is robust and authorative
* it's definitely low-level
* it's curt
* reading it often requires more concentration than is usual for a programming language, because you need to track changes to state over time *in your head*
* Visual Studio has an [extension for PS](https://marketplace.visualstudio.com/items?itemName=mxschmitt.postscript).
* There's an [Eclipse-based plug-in](https://marketplace.eclipse.org/content/postscript-development-tools#details) 
* Notepad++ has built-in syntax highlighting for PS 
* debugging PS can be very rudimentary. 
* the conversion from PS to PDF for the final output is a non-issue, since it's handled by well-known and stable tools (Ghostscript and Adobe Acrobat/Distiller).
* there appears to be no significant community of users producing libraries for it
* there's little discussion of PS amongst modern programmers. Given the ubiquity of PDF and EPS, this is rather strange.
* outputs in English and French that can use the 8859-1 (Latin-1) character encoding are straightforward (see below).
* outputs in Chinese, Japanese, Korean and so on are possible, but more complicated.

In Language Level 3, there are about 385 operators listed in the manual.
Initially, this gives you the impression that PS is large and complex. 
But after using it for a few weeks, it seems that the opposite is the case: 80% of the time, you use 20% of the operators. 
As usual, it's not only about the language, but the language <em>plus the libraries</em>.
In a sense, PS is all language and few libraries, so you could argue that its overall size is small.

This makes sense: PS is a special purpose language, designed mainly for printing documents.
There are no libraries for date-time control, regular expressions, threads, and so on.

There are some common items that you may feel should have been part of the language:
* simple string concatenation (!)
* text flow
* table layouts

PostScript seems to have been designed to implement the core operations as robustly as possible, and to 
leave higher-level considerations such as text flow to the application programmer.  


## Producing Documents Programmatically With PS

The simplest case for using pure PS to generate documents is when:
* there are no large chunks of text, which need to have their text-flow managed
* the general emphasis is on data, in some sense, instead of large amounts of text
* you can use its drawing capabilities to make illustrations (graphs, charts, diagrams, and so on)

For example, reports generated by typical systems can fall into this category.

In PS, managing text becomes more complicated when: 
* the blocks of text are large, and can flow onto subsequent pages (widows, orphans, and so on)
* the blocks of text change fonts in mid-stream (because you have to interpret your own <em>ad hoc</em> markup mechanism)

For general typesetting, pure PS is entirely at the low end of the spectrum. 
At the other end would be tools like Adobe's [InDesign](https://www.adobe.com/ca/products/indesign.html).


## The 'Lifecycle' of a Glyph, and Encoding
In PS, the `show` operator marks a page with text characters.
Here, a simple literal is passed the operator:

```
(Hello) show
```
 
It's important to understand how the system marks the letter 'H' on the page:
* the system **passes a number to `show`**, not a character! **There is no 'character' data type in PS.** *In PS, 'string' means a string of numbers, all in the range 0..255.*
* the number comes *from the file's encoding of the character*. For English/French, your file encoding should very likely be Latin-1 (8859-1).
* that number is passed to the `show` operator. PS calls this number the *character code*.
* the current font has all of its info in a font-dictionary. One entry in that dictionary defines how it maps an incoming 
number (the character code) to a glyph that it knows how to draw.
* that entry has `/Encoding` as its key, and its value is an array containing 256 glyph names. **This array is called the encoding vector**.
* the names in the encoding vector are the names of glyphs supported by the font, such as `/Hsmall` and `/hyphen`
* **the incoming character code is used as an index into the encoding vector**
* a glyph name is returned
* it's used to look up the drawing instructions for that glyph
* the glyph is drawn at the `currentposition`
* when finished, the `currentposition` is updated by the font to be a new position, in preparation for the next glyph
* the update to the new position is a displacement *dx* and *dy* from the initial position that was originally passed
* each glyph can have a different displacement
* the displacement can even depend on the surrounding characters (ligatures, Arabic characters)
* for English, *dx&gt;0* and *dy=0* (flow from left to right)
* for Arabic or Hebrew  *dx&lt;0* and *dy=0* (flow from right to left)
* for vertical Chinese, *dx=0* and *dy&lt;0* (flow from top to bottom)
* for monospaced (fixed width) fonts, the displacement is the same for every glyph. Most fonts are not monospaced.

Many fonts support fewer than 256 glyphs.
In that case, the encoding vector will have entries of `.notdef`.
It can be repeated in many places in the encoding vector.
If the system passes in a character code that corresponds to a `.notdef` entry, then the font is required to define a special glyph to act as a placeholder.
This is the source of some weird characters that you sometimes see when the encoding is not correct in a system. 




## Re-encoding Fonts to Latin-1 (8859-1)
PS was first published before [Unicode](https://en.wikipedia.org/wiki/Unicode) was invented.
It's possible to use it to print documents in languages such as Chinese, with large character sets, but 
it's more complicated than creating a document using a Latin-1 (8859-1) encoding. (I've never done that, so I have no insight on that.) 

For documents in Western European languages like English and French, here are the steps that you need to do:
1. Your PS files need to be encoded using Latin-1 (8859-1).
2. Every time your application reads/writes a PS file, the Latin-1 encoding needs to be used.
3. In your PS code, every time you start using a font, you need to "re-encode" the font to use Latin-1.

The re-encoding of a font is necessary since the default encoding is an old Adobe encoding that nobody uses anymore (I think).
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

An example of creating a path:
```
newpath 
12 50 moveto
..more path construction here..
..`closepath` often comes at the end..
```

The endpoint of the current path is called the *current point*. 

There are a number of operators that interact with the current path.
**It's important to know the details of how operators interact with the current path.**

*Creates* a new path:
* `newpath`
* `moveto` starts a new *path* or *subpath* at a new position, but doesn't add a line segment. It's like moving a pen above paper, without making a mark.

*Appends* to the current path :
* `lineto`, `curveto`, `arc` (and similar operators, such as `rlineto`, which use relative displacement) 
* `closepath`
* `show` (for text) displaces the current position to a place nearby the drawn text (as defined by the font)   

*Consumes*  the current path (and clears it with an implicit `newpath`):
* `stroke`
* `fill` - this also implicitly calls `closepath` when needed!

Note: 
* having a `newpath` immediately after a `stroke` or `fill` is redundant.
* `stroke` paints the outline of a closed path, while `fill` paints the interior, without painting the outline.

*Copies* the current path:
* `clip` creates a clipping region using the current path, and leaves the current path in place

*Internally* creates and uses its own path:
* `rectfill` and `rectstroke`



## The Drawing Pattern

* set up the coordinate system: `translate` (very common), `scale`, `rotate` (less common)
* create a path
* `stroke` and/or `fill`, and sometimes use a `clip`

The entire sequence is often surrounded by `gsave` and `grestore`. 
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

The first line has two operators that create an anonymous dictionary, and then put it on top of the dictionary stack. 
In this case, the dict is initialized to hold 10 items; but, if you accidentally use it to store more than 10 items, the system 
will automatically re-size the dict as needed (except in Language Level 1). 
Each `begin` is paired with an `end` operator at the end of the proc.
The `end` operator simply removes/destroys the temporary dictionary, when it's no longer needed.

This is just the proper use of the dictionary stack, *whose primary purpose is to be a dynamic namespace*. 

Warning: this is not 100% local, in the following sense: if proc A calls a helper proc B, then proc B sees the exact same dictionary stack as proc A.
**In the language of object-oriented programming, the "local" data behaves more like an object's *field*, rather than a local variable defined in a method.**



## Idiom: Move Data on the Stack into the Current Namespace
There is a trade-off between using data on the stack directly, versus pulling the data off the stack and storing it in a dictionary.
Among the more experienced, there may be a tendency to use the stack directly, perhaps rearranging the order of things using built-in stack operators. 

For the following proc, say it has two arguments passed to it, called *dx* and *dy*.
This implementation defines two items in a temporary dictionary (and thus adds them to the current namespace).
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

It's important to **note that none of this is needed  if *dx* and *dy* are already in the current namespace when `my-delightful-proc` is called.**
In that case, it's neither necessary nor desirable to pass *dx* and *dy* as params. 
(An exception: if you want to give the data different names for some reason.)

## Idiom: Save/Restore for Page Independence
**Adobe very strongly recommends having pages independent of each other.**
The typical way to accomplish this is to wrap the code that generates a page in a ``save\restore`` pair.
In this way, the state of memory (local VM, not global VM) is saved at the beginning of the page, and resurrected at the end, such that each page 
starts with local VM memory that is in the exact same state.

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

`(some-code.ps) run`

Ghostscript has a variation on this idea:

`(some-code.ps) runlibfile`

  
## Idiom: Get Clean Joins With closepath
When you construct a closed path, instead of closing it explicitly, you should usually 
close it with the `closepath` operator. 
The reason is that it makes a visually pleasing join by default.


## Idiom: Use Whitespace to Increase Legibility
PS can be hard to read. 
You have to sometimes concentrate on the visualizing what operators are doing, and 
track the state of the operand stack in your head. 

Example:
```
{cell-dx 58 pct cell-dy 23 pct translate
 data (tidal-range) get data (tides) get cell-dx 40 pct cell-dy 50 pct daily-tide-details} draw
```

If you add simple whitespace, to group together related items, then it's a bit easier to read. 
You can also group together related items using line-breaks:
```
{cell-dx 58 pct   cell-dy 23 pct   translate
 data(tidal-range)get   data(tides)get    
 cell-dx 40 pct   cell-dy 50 pct 
 daily-tide-details} draw
```

## Font Selection
PostScript supports different [font types](https://en.wikipedia.org/wiki/PostScript_fonts).

<em>"Ghostscript can use any Type 0, 1, 3, 4, or 42 font acceptable to other PostScript language interpreters or to ATM, including MultiMaster fonts. 
Ghostscript can also use TrueType font files."</em>

This makes no mention of OpenType OTF fonts. Ghostscript [may support them](https://bugs.ghostscript.com/show_bug.cgi?id=694790), however.

The main ones here are:
* Type 1
* Type 42, which means TrueType fonts

PostScript docs make no explicit mention of OTF fonts, but they appear to be supported in Adobe Acrobat's Distiller ([example](https://helpx.adobe.com/ca/acrobat/using/pdf-fonts.html)), 
a tool which translates PS files into PDF files.
This may be related to the fact that the differences between OTF and TrueType are apparently small, OTF being an extension of TTF.

Be aware that font file extensions (.ttf, .otf, and so on) are sometimes not a good indicator of the actual precise font type.

Surprisingly, Adobe is [phasing out support for Type 1 fonts in some tools](https://helpx.adobe.com/ca/fonts/kb/postscript-type-1-fonts-end-of-support.html).

There's no standard set of fonts required by the PostScript language.

Ghostscript has 35 [free-to-use fonts](https://en.wikipedia.org/wiki/Ghostscript#Free_fonts), plus some other non-free ones.
Many open-source projects rely on fonts from Ghostscript.

You need to tell Ghostscript where to find your font files. 
You can control this in different ways. For example:

* set the ``GS_FONTPATH``environment variable
* edit its ``Fontmap.GS`` config file, and add a line stating the name of the font and the corresponding file location


## Idiom: Use a Proc For Drawing
Drawing operations are usually of the form

```
..translate..
gsave
..draw..
grestore
```

It's possible to put that pattern into a proc, as a template:
```
% pass a procedure 
% draw something, and then have this proc restore the graphics state
/draw {
  gsave 
    newpath % ensures the currentpath/point is empty
    exec  % execute the passed proc
  grestore
} bind def

% the caller looks like this:
100 50 translate
{
  ...
} draw 
```


## Idiom: List The Contents of a Dictionary

```
% for debugging only
% pass a dict; shows its keys and values on stdout
/list-dict {
 { == ==  } forall
} bind def
```

## Show the currentpoint

Knowing "where you are" on the page is often important.
Here's a proc that draws a little circle and cross at the `currentpoint`, in red. 

```
% size-r 
% debugging - show the location of the currentpoint (in red)
% the currentpoint must exist
% pass a representative size for the mark to be made
/show-currentpoint {
gsave
 /r exch def
 currentpoint   
 /y exch def
 /x exch def

 r 0.1 mul setlinewidth
 0.5 0 0 setrgbcolor
 newpath            % we want the currentpoint, but not the currentpath           
 x y moveto
 r 0 rmoveto
 x y r 0 360 arc 
 stroke
 
 x y moveto         % back to start
 r neg 0   rmoveto 
 r 2 mul 0 rlineto  % horizontal
 r neg 0   rmoveto  % back to start
 0 r neg   rmoveto
 0 r 2 mul rlineto   % vertical
 stroke
grestore
} def
```


## Use CMYK for Print, RGB for Screens
Adobe has the idea of *color spaces*. 
You almost always use the CMYK color space for print outputs, and RGB for an output being viewed on a screen. 



 
## Consider Using Percent Coordinates
Probably the first decision in many projects is the choice of dimensions for the page.
If you code explicitly to those dimensions, then that one decision will be all over your code.
That one decision becomes more or less locked-in.

**Instead of using absolute units, you should consider using percentage units instead.**
In that case, the decision about the actual format can be implemented in a single line of code,
with all other coordinates being defined relative to some base as a percentage.

**One of the main advantages of PostScript is that of scalability. 
It seems a shame to throw that away by locking in to a single format.**
(Note that using the `scale` operator to change size only works if the aspect ratio remains the same.)   

In the web, [pages that are responsive](https://en.wikipedia.org/wiki/Responsive_web_design) to 
changes to screen size and orientation have become ubiquitous.
The exact same idea can be applied to print. 

When you use percentages instead of absolute units, it will usually give you 90% of what you need when the aspect ratio changes significantly.
Typically, you will still need to make small adjustments here and there to get it just right.
The same is true on the web. 
For example, the size of margins, *as a percent of the screen dimensions*, is usually larger when the screen is physically larger.
 


## Margins That Are Sensitive to the Position of the Binding
Many, many books published these days have margins that are the same left-right.
This often creates annoying problems for the reader, if the text is close to the binding.
If your output is to be bound, consider making your margins asymmetric, with more margin near the binding.
The `setpagedevice` operator has settings to control this, but it may be best to do it explicitly in your code, to ensure its device-independent.
(See Table 6.4 in the docs, and the `ImageShift` setting.)



## Text Layout and Markup in the Driver
Two common issues:
* laying out large amounts of text - columns, alignment, justification, page breaks, widows/orphans
* in text flow, having to vary the font for italics/bold and so on

Where is this logic implemented? There are ways to do it in PS, but it's not built into the core language.
The PS docs encourage you to implement all of this in the program that generates PS code (in the *driver*).

From the Green Book:

*"The application should always control placement of text, and should know the character widths beforehand."* 

That being said, you can certainly implement basic text flow completely in PostScript.
If it's done in the driver, then you'll likely need to read and parse font metric files in order 
to compute the `currentpoint`.
(If you restrict yourself to a monospaced font, you can avoid this.)


## Images Are Usually Referenced With Full Path Names (?)
Ghostscript has a `runlibfile`, and you can configure Ghostscript to find PS files in your project's directories.
I'm not certain, but it seems that the same mechanism is not available when opening image files with the `file` command.
In that case, you need to specify the full file path.


## Embed fonts in PDFs
It's always safest to embed fonts in your output PDFs.
Ghostscript can create a PDF output using `-sDEVICE=pdfwrite`. 
Be aware that, if you're using one of Ghostscripts core fonts, then by default the font will NOT be embedded in the output PDF.
[Override that default behaviour](https://stackoverflow.com/questions/41205112/ghostscript-stubbornly-refuses-to-embed-fonts) by adding this to your PS: 

  `<< /NeverEmbed [ ] >> setdistillerparams`

  
## Always setstrokeadjust to true
When a straight line is finally rasterized, there are off-by-one pixel issues that can arise.  
For example, a grid of lines that are supposed to have the same line width can actually vary slightly in width.
To minimize such issues, use:

`true setstrokeadjust`

## Convert a Character Code to a String
A character code is a number. Sometimes you are given such numbers by a built-in operator. 
You usually want to convert that number back into a string.
The trick is to use the `put` operator. For example, the Blue Book has this snippet on page 167, Circular Text:
 
  `( ) dup 0 charcode put`
  
I'm not sure why `dup` is present here.
 
## Stroke before clip
If you have a path that you want to both `stroke` and `clip`, you likely want to perform the stroke first.
If you clip first, then half of the stroke will likely be cut off.

## Accessing the Proc's in a Library
Here's some [example code on stackoverflow](https://stackoverflow.com/questions/79454801/postscript-defining-a-namespace-for-a-library).