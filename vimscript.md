autoscale: true
# [fit] Vimscript

---

# [fit] Or Vim script

---

# [fit] Or VimL

---

# [fit] Or VimLang

---

# Why learn Vimscript?

---

# It's annoying

- Variable scoping is weird
- Hard to test
- Hard to debug
- Vim makes regexes more complicated

---

# Ok but really

- Productivity is fun? Maybe?
- Glutton for punishment
- You like collecting languages
- Make your life easier

---

# This is not

- A lesson in vimrcs
- Vim cool tips and tricks
  - I think Nick Tomlin has some idea for this
- Going to focus on writing in the lang and plugins

---

# Vim help files

```vim
:help if
:help search
:help getline
:help write-plugin
:help internal-variables
:help help
:help usr_41
:help E171
```

^ Vim has a great collection of helpfiles. The docs are pretty extensive and
incredible and baked into vim. There is help on help itself, if a plugin
includes a doc, the help files (which are just .txt) are built in. There is a
good system for finding help by keywords and overall, it's just really nice.

---

# `:help if`

```
:if {expr1}                     :if :endif :en E171 E579 E580
:en[dif]                Execute the commands until the next matching ":else"
                        or ":endif" if {expr2} evaluates to non-zero.

                        From Vim version 4.5 until 5.0, every Ex command in
                        between the ":if" and ":endif" is ignored.  These two
                        commands were just to allow for future expansions in a
                        backward compatible way.  Nesting was allowed.  Note
                        that any ":else" or ":elseif" was ignored, the "else"
                        part was not executed either.

                        You can use this to remain compatible with older
                        versions:
                                :if version >= 500
                                :  version-5-specific-commands
                                :endif
```

^ Example help section for if

---

# Code snippets
Some code snippets in this presentation will use a leading `:` and others won't.

`:` can be used in any vim session at anytime to run these

When they are run without is when they are part of a vimscript.

^ Quick comment on some of the code snippets that are shown. Sometimes I will
show them with a leading colon. That is just to indicate it may be easier to run
that command just in a normal vim session to see the results. It's basically the
same as an IRB or pry session where you can run a few commands easily, but when
building up functions, you would want to use a file.

---

# One more thing:

Vim does have some normal parts

```vim
if
while
for
==
||
&&
```

[.footer: `:help if, :help while, :help for, :help ==, :help ||, :help &&`]

^ This is not meant to be exhaustive. Vim has things like if, while, for,
equals, or, and, etc. I am not describing how all those work. Some of them are a
little specific to vim, but they work pretty in line with what you'd expect. I
want to share the things that are more vimmy.

---

# Variables

Variables are (in most cases) defined with `let`.
They are also reassigned with let.

```vim
let myvar = "Hello"
echo myvar  " => Hello
let myvar = "Hi"
echo myvar " => Hi
```

[.footer: `:help let, :help echo`]

^ Variables are defined with a let. One thing to note is that if you want to
reassign a variable, you still need to use let.

---

# Variable Types

- Number
- Float
- String
- Funcref
- List
- Dictionary

[.footer: `:help variables`]

^ Here are the variable types, also nothing special expect to note that you can
store functions in vim variables, and vim also has a version of eval, so...go
nuts with your vim metaprogramming.

---

# Variable Scope

```
- buffer-variable|    b:	  Local to the current buffer.
- window-variable|    w:	  Local to the current window.
- tabpage-variable|   t:	  Local to the current tab page.
- global-variable|    g:	  Global.
- local-variable|     l:	  Local to a function.
- script-variable|    s:	  Local to a |:source|'ed Vim script.
- function-argument|  a:	  Function argument (inside a function).
- vim-variable|       v:	  Global, predefined by Vim.
```

[.footer: `:help internal-variables`]

^ Variable scope is a little interesting in vim. There are 8 variable types, the
ones listed her. If you aren't fully familiar with these terms, a buffer is a
file that is open. A window is alive while you are looking at a buffer, but if
you switch files or close a window, the window dies but the buffer is still in
the background. If you used buffer explorer, you can know that those are the
buffers.

^ Tabpage is, if you use tabs, the windows that make up the tab.

^ Global and vim are mostly similar, a normal global variable, available
everywhere, and v just means they are predefined by vim.

^ Script is available to a script (i.e. plugin) and local is available to a
function. Argument variables are the args to a function, but are a little
different.

^ These letters are actually prefixes used when defining variables and are
required in most cases.

---

## Global Variables

Checking for plugin loading:

```vim
if exists("g:loaded_my_plugin")
  finish
endif
let g:loaded_my_plugin = 1
```

Plugin configuration:

```vim
let g:ale_enabled = 0
```

[.footer: `:help global-variable, :help write-filetype-plugin`]

^ This is commonly used within plugins to check if they are loaded so as to not
reload them again. It is also used in vimrcs as a way of doing plugin
configuration.

---

# Local Variables

```vim
function Hello()
  let name = "Mario"
  echo "Hello " . name
  let l:name2 = "Peach"
  echo "hi " . name2
endfunction
```

[.footer: `:help local-variable`]

^ The l prefix is not required within a function, but you can use it if you
would likely to be consistent. However, you are required to use it if you want
to create a variable with the same name as a global vim command, count for
example.

---

# Script Variables

```vim
let s:score = 0
function HomeRun()
  let s:score += 1
endfunction
```

[.footer: `:help script-variable, :help for`]

^ We can define a script variable, and increment it. It will be stored within
that scripts list of variables, so if we were making a plugin that counted or
track or anything, we can keep that variable specific to our script. You can
also make functions specific to a script, making them not callable outside the
script. It is not uncommon to make all your functions script specific, as to not
collide with function names, and then use prefixed commands or other methods as
your script API of sorts.

# Variable Dictionaries

```vim
for k in keys(s:)
  unlet s:[k]
endfor
```

[.footer: `:help script-variable`]

^ Variables are actually just kept in a dictionary, and you could iterate over
them.

---

# Functions

- Start with a capital letter by convention (or be local to script)
- Called via `call` or an expression like `:echo MyFunc()`
- Implicit return of 0 (which is falsey)

[.footer: `:help function, :help E124`]

^ Read slide

---

## Defining a function

```vim
function Hello()
  echo "How is it going?"
endfunction
```

[.footer: `:help function, :help E124`]

^ Describe function

---

## Calling a function

```vim
call Hello() " => How is it going?
echo Hello() " => 0
```

[.footer: `:help function, :help call`]

---

# Calling a function

```vim
1  Book
2  Apple
3  Raven

function! First()
  echo split(getline('.'), '\zs')[0]
endfunction

:1,3call First() " =>
B
A
R
```

^ Functions and commands in vim can both take ranges. Sometimes the functions
and commands can handle those by doing something with between the lines that are
passed in, otherwise it will just execute the command for each line. (Describe
this function).

^ If the function keyword ends with a bang, that just means it will overwrite
other functions with the same name, which shows the importance of making
variable scopes appropriate.

---

# Function arguments

- Use the `a:` variable prefix
- Are normally accessed via keyword
- Can be passed in like a splat, and are accessed via index

---

# Function arguments

- Can't reassign arg variable

```vim
function Test(name)
  let a:name = "Minnie"
endfunction

call Test("Mickey") " => Cannot change read-only variable 'a:name'
```

[.footer: `:help function-argument`]

---

# Function arguments

```vim
call Foo("dopey", "sneezy", "bashful", "doc", "grumpy", "happy", "sleepy")

function Foo(bar, baz, ...)
  echo a:bar "      => dopey
  echo a:baz "      => sneezy
  echo a:0 "        => 5
	echo a:1 "      => bashful
	echo a:000[0] " => bashful
	echo ""
  for s in a:000
    echon ' ' . s
  endfor
  " => bashful doc grumpy happy sleepy
endfunction
```

[.footer: `:help function-argument`]

^ So if we have 2 named variables, they are positional when we call the
function, however they are named when we reference them.

^ Additionally, notice that a:0 is the count of our splat and that a:1 and
a:000[1] are equivalent. And that grabbing the first one out isn't like a stack,
so if we print the splat, bashful also prints.

---

# Function argument ranges

- When using a range, you get `a:firstline` and `a:lastline` automatically

```vim
function Cont() range
  execute (a:firstline + 1) . "," . a:lastline . 's/^/\t\\ '
endfunction
4,8call Cont()

=> :5,8s/^/\t\\ '
```

[.footer: `:help function-range-example`]

^ In this case the execute is like eval, and it is taken the first line number
(in thsi case, 4) and adding 1, so it runs that last command which replaces the
beginning of each line with a tab, a slash and a space.

---

# Partial Functions

Functions in vim can be composed as partials

```vim
func Callback(arg1, arg2, name)
...
let Func = function('Callback', ['one', 'two'])
...
call Func('name')
```

[.footer: `:help function()`]

^ For anyone who has gone through the tech interview, if you remember partial,
you can do it in Vim. You can store the function in a variable with only the
first 2 args defined, and then call it later providing the last arg.

---

# Commands

A phrase that tells vim to do something, often will be the calling of a
function.

```vim
PlugInstall
Files " fzf
TestLast
```


[.footer: `:help command`]

^ Commands will just be run as is, without the call or execute command.

---

# Writing a command

```vim
function s:Hello()
  echo "How is it going?"
endfunction

command Hello :call s:Hello()

:Hello " => How is it going?
```

[.footer: `:help command, :help E174`]

^ So we have our good old hello function and we define a command, also called
Hello. Again, if we use a bang at the end, it will overwrite existing functions.
So when calling the hello command, we don't need call or execute. These will
also be made available globally in vim. Also notice the E174, which is sort of
like a subsection.

---

# Command args

Commands take a variety of commands:

- -nargs (0, 1, *, ?, +)
- -complete (augroup, buffer, color, command....file, file_in_path, shellcmd...)
- -range (%, N)
- some other special cases 

[.footer: `:help command-nargs, :help command-completion, :help command-range`]

^ It is not as commond for a command to take an arg, vs a function, but it is
possible. It defaults to 0, but you can allow 1 or many or 0 or 1 or the plus is
that args are required but any number.

^ Complete adds autocomplete functionality, for which there are many options.

^ Range determins whether a range can be passed to a command. If you have
functions/commands that can work on single lines or ranges, it may be helpful to
actually defined multiple commands, one that takes a range (maybe prefix it with
V) and one that doesn't if you want slightly different behavior.

^ There are some other special cases you can check with the help command.

---

# Mapping

You have likely seen this in a vimrc, but you can map keystrokes to various commands

- `map`
- `nmap`
- `vmap`
- `xmap`
- `imap`

 ```vim
map <silent> <LocalLeader>rf :wa<CR> :TestNearest<CR>
```

[.footer: `:help map-commands, :help map-modes, :help map-<silent>`]

^ Map is all modes, nmap is normal mode (not insert) vmap is Visual and Select
while xmap is just visual, imap is insert

^ Here is the mapping we use for doing leader rf for running a ruby test. The
silent flag says not to echo anything, commond when mapping. LocalLeader is
something you have defined, which is different than leader (although they can be
the same). And notice that what we do is map rf to another set of keystrokes. It
types colon then w then a, then it hits return. So it actually saves all open
buffers and THEN calls the TestNearest command with colon and hitting enter.

---

# Remapping

You may have seen these as well:
 - `:noremap, :nnoremap, :vnoremap`

This determine whether or not to follow mappings recursively.

```vim
:map j gg
:map Q j
:noremap W j
```

[.footer: `:help recursive_mapping`]

^ In this case, when you hit j, it will go to the bottom of the file. If you hit
Q, since it is using the normal map command, it will call j which then calls gg
and goes to the bottom of the file. However, when using noremap, if we hit W, it
will do what the default j command is.

---
# <SID>

Something to note is that mappings occur outside the scope of a vimscript.

`nmap <silent> ]w :call s:Hello()<CR>`

Produces:

`E81: Using <SID> not in a script context`

Instead use:

`nmap <silent> ]w :call <SID>Hello()<CR>`

[.footer: `:help <SID>`]

^ Mappings can be done in a script or a vimrc or init.vim. Regardless of where
they are run, they are not really run in the context of a script or plugin, even
if they are defined in that file. However, if you do the write thing and scope
your function to your script and then try to map it, you see that E81 error.

^ Instead, we use this <SID> or S ID prefix which internally will map this
command to the scripts prefix, allowing you to call script local functions.

---

# Library Functions

From `janko-m/vim-test/autoload/test/ruby/rails.vim`

```vim
function! test#ruby#rails#test_file(file)
```

---

# Line Commands
Vim has a variety of commands for accessing and working with lines:

- `getline('.')`
- `match(getline('.'), '\%(Hello\|Hi\)'`
- `search('World')`
- `searchpair('if', '', 'end')`
- `line('.')`
- `cursor(1,1)`

[.footer: `:help getline, :help line, :help match, :help search, :help searchpair`]

^ Here are some of the commands you can use for moving around a file. Describe
each

---

# Line symbols
- `.` - cursor
- `$` - last line
- `#` - line number
- `'x` - postition of mark x
- `w0` - first line visible
- `w$` - last line visible
- `v` - start of visual area

[.footer: `:help line`]

^ Here are the various symbols that can be passed to getline or line and other
functions I'm sure. Describe them.

---

# Patterns

Vim basically has their own implementation of regex, called patterns.
Complicated but powerful:

```vim
let s:end_start_regex =
      \ '\C\%(^\s*\|[=,*/%+\-|;{]\|<<\|>>\|:\s\)\s*\zs' .
      \ '\<\%(module\|class\|if\|for\|while\|until\|case\|unless\|begin' .
      \   '\|\%(public\|protected\|private\)\=\s*def\):\@!\>' .
      \ '\|\%(^\|[^.:@$]\)\@<=\<do:\@!\>'
```

^ This is vim ruby's pattern for finding what triggers an end to be created
First line is case insensitive, and that a line stars with spaces or one of
those symbols, or the shovelers, or a colon and a space. Then it has one of
those words or it has public/protected/private with def in the same line not
followed by a colon, and then it has a do that is not at the beginning of the
line or preceded by one of those symbols and doesn't have a colon at the end.

^ This is used for search and maneuvering and all sorts of stuff.

[.footer: `:help pattern`]

---

# So you wanna make a plugin?

- myplugin/autoload/mylib.vim => libraries
- myplugin/compiler/ruby.vim => with compiler
- myplugin/doc/myplugin.txt => docs
- myplugin/ftplugin/ruby.vim => lang specific
- myplugin/indent/ruby.vim => indent rules
- myplugin/plugin/myplugin.vim => "regular"
- myplugin/syntax/ruby.vim => syntax rules

[.footer: `:help write-plugin, :help write-filetype-plugin, :help write-compiler-plugin, :help write-library-script`]

---

# Vim Ruby Block Helpers

` vim-ruby-block-helpers/ftplugin/ruby.vim`

```vim
if exists("g:ruby_block_helpers")
  finish
endif
let g:ruby_block_helpers = 1
```

---

# Get Some Patterns

```vim
let s:end_start_regex =
      \ '\C\%(^\s*\|[=,*/%+\-|;{]\|<<\|>>\|:\s\)\s*\zs' .
      \ '\<\%(module\|class\|if\|for\|while\|until\|case\|unless\|begin' .
      \   '\|\%(public\|protected\|private\)\=\s*def\):\@!\>' .
      \ '\|\%(^\|[^.:@$]\)\@<=\<do:\@!\>'
let s:end_pattern = '\%(^\|[^.:@$]\)\@<=\<end:\@!\>'
```

---

# RubyBlockNext Function

```vim
function! RubyBlockNext(...)
  norm! m'

  if len(a:args) > 0 && a:args[0] == 'v'
    norm! V
  endif

  let flags = "W"
  if match(getline('.'), s:end_pattern, flags) == -1
    call RubyBlockEnd()
  endif

  call search(s:next_block_pattern, flags)
endfunction
```

---

# RubyBlockNext Command

```vim
command! RubyBlockNext :call RubyBlockNext()
command! -range VRubyBlockNext :call RubyBlockNext(visualmode())
```

---

# RubyBlockNext Mapping

```vim
nmap <silent> ]b :RubyBlockNext<CR>
xmap <silent> ]b :VRubyBlockNext<CR>
```

---

# Adding it

```vim
Plug 'dewyze/vim-ruby-block-helpers'
```

---

# Shameless Plug

This is in our dotfiles!

```
]b Next Block
[b Previous Block
]p Parent Block
]c Parent Context (Rspec)
]s Block Start
]e Block End
```

---

# Hierarchy

```
]h Block Hierarchy

describe "foo" do
  context "bar" do
    it "baz" do
```

---

# Environment

```
]v Block Environment

describe "foo" do
  let(:thing1) { "thing 1" }
  context "bar" do
    @thing2 = Thing2.new
    it "baz" do
```

---

# Vimdoc

Github: github.com/google/vimdoc

---

# Other Things

```vim
:map " Show all current mappings
:registers Show where you have yanked stuff into
:mark " ma Or mA, then 'a or 'A
```

`autocmd FileType ruby autocmd BufWritePre <buffer> %s/\s\+$//g`

Check our your own vimrc!

[.footer: `:help map, :help registers, :help mark`]

---

# Thank you!
# Questions?

Slides: dewyze/vimscript_presentation
