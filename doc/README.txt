Fellow vim fans,

There are syntax files on my website (www.zimmerdesignservices.com) Primetime
tcl (pt_<version>.vim), dc_shell tcl (dctl_<version>.vim) and both combined
(dctlpt_<version>.vim).  

There is now a formality file as well (fm_<version>.vim).

A file for einstimer/booldozer tcl (einsstd.vim) is also available.

Extract them and put them wherever you like, then you have to hook them in.

Hooking them in via the ~/.vim directory
----------------------------------------

The easiest way to hook them in is to put them in the directory
"~/.vim/syntax" (save them as "dctl.vim" and "pt.vim").  You can then hook
them in by creating a "filetype.vim" file in "~/.vim" with the following:

au BufRead,BufNewFile *.dctl            set filetype=dctl
au BufRead,BufNewFile *.pt            set filetype=pt

On a PC, ".vim" is called "vimfiles", and there are two possibilities for it's
location.  On my PC, when I launch from an icon, it looks at "Documents and
Settings\<user>", one level above "My Documents".  When I launch from cygwin,
my home dir is set to "My Documents", so vim looks for vimfiles there.  I
suppose I could sort this mess out somehow, but the easiest thing is to just
create it in two places.


If you store your dctl and pt files as .tcl files
-------------------------------------------------

If you store all your dc/pc and pt scripts as .tcl files, you'll have to
tell vim what the filetype is.  Use the "hooking them in via ~/.vim 
directory" approach above, then put one of the following as a comment at
the beginning of the file:

# vim: filetype=dctl
# vim: filetype=pt

Alternatively, you can just issue the command from vim:

: set filetype=dctl
: set filetype=pt

Or, you can make this apply to *all* files of that type by putting an entry
in ~/.vim/filetype.vim:

au BufRead,BufNewFile *.tcl             set filetype=dctlpt


Hooking them in via the .vimrc
------------------------------

This is the way I had done it for years, until I discovered the .vim technique:
I use the ".pt" suffix for PrimeTime files and ".dctl" for
dc_shell tcl-mode files.  So, I have the following lines in my .vimrc to
automatically invoke the correct syntax file:

autocmd BufNewFile,BufRead *.pt set formatoptions=croql tw=0 expandtab autoindent comments=:#
autocmd BufNewFile,BufRead *.pt source $bin/vim/syntax/pt.vim
autocmd BufNewFile,BufRead *.dctl set formatoptions=croql tw=0 expandtab autoindent comments=:#
autocmd BufNewFile,BufRead *.dctl source $bin/vim/syntax/dctl.vim

As you can see, I store the syntax file in directory "$bin/vim/syntax".
Change this as appropriate.  I also assume that you save the files as 
"dctl.vim" and "pt.vim" (without the version numbers).

Dictionary files
----------------

The tarballs also include .dict dictionary files.  To make things simple, they
are automatically loaded by the syntax file.  This isn't quite kosher as far
as the vim gods are concerned, but it does work without any effort on your
part.

The clean way to hook in is by commenting out my load in the syntax file (look
for the "complete+=" command near the end) and creating
a <language>.vim file in .vim/after/syntax, (vimfiles/after/syntax on the PC)
that looks like this:

set complete=k~/.vim/syntax/pt.dict 

To use the dictionary file, just start typing a command and hit ctl-n.  Vim
will give you the first dictionary word (in this case, command) that matches.
ctl_n gets the next.  ctl-p goes backwards.  Very handy.


Color Management
----------------

As a consultant, I end up writing code in lots of different environments.
Sometimes, I find that a client's machine has the colors set in some wierd way
(like pale green on paler green) and is just unreadable.  Or it might be
readable, but isn't the color that my old, tired brain is used to.  To get
around this, I force the color mapping in my .vimrc file.  The code I use
looks like this:

hi NonText guifg=Blue gui=Bold
hi Directory guifg=Blue
hi ErrorMsg guifg=White guibg=Red
hi IncSearch gui=reverse
hi Search guibg=Yellow
hi MoreMsg guifg=SeaGreen
hi ModeMsg gui=Bold
hi LineNr guifg=Brown
hi Question gui=bold guifg=SeaGreen
hi Question gui=bold,reverse

hi StatusLine gui=bold,reverse
" Causes multi-window spit to not have divider in inactive split
"hi StatusLineNC gui=bold 
hi StatusLineNC gui=bold,reverse

hi VertSplit gui=reverse,bold
hi Title gui=bold guifg=Magenta
hi Visual gui=reverse guifg=Grey guibg=fg
hi VisualNOS gui=bold,underline
hi WarningMsg guifg=Red
hi WildMenu guifg=Black guibg=Yellow

hi Folded guifg=DarkBlue guibg=LightGrey
hi FoldColumn guifg=DarkBlue guibg=Grey
hi DiffAdd guibg=LightBlue
hi DiffChange guibg=LightMagenta
hi DiffDelete guifg=Blue guibg=LightCyan gui=bold
hi DiffText guibg=Red gui=bold
hi Cursor guifg=bg guibg=fg
hi lCursor guifg=bg guibg=fg
hi Comment guifg=Blue
hi Constant guifg=Magenta
hi Special guifg=DarkBlue
hi Indentifier guifg=DarkCyan
"hi Statement guifg=SlateBlue gui=bold
hi Statement guifg=DarkRed gui=bold
hi PreProc guifg=Purple
hi Type guifg=SeaGreen gui=bold
hi Underlined guifg=SlateBlue gui=underline
hi Ignore guifg=bg
hi Error guifg=White guibg=Red
hi Todo guifg=Blue guibg=Yellow

" Variables:
"hi Identifier guifg=DarkGreen  
hi Identifier guifg=DarkCyan  



Details
-------

The syntaxing can handle optional arguments and all their legal abbreviations.

There is also a script called "mk_tcl_syntax.pl" that can create syntax files 
from the users procs.  It is now included in the archive.


New features:
  Built-in variables are given special treatment (new color) rather than just
  treating them as commands.  This is now set up so that accessing them
  without the leading $ in a set or global command will match as well.
  Attributes are also given special treatment, but only inside a filter
  command, -filter option, or a get_attribute command.

  Command abbreviations now fully supported!
  All command abbreviations should work now.  They can be disabled by doing:
    let fm_syntax_use_cmd_abv = "0"
    let pt_syntax_use_cmd_abv = "0"
    let dctl_syntax_use_cmd_abv = "0"
    let dctlpt_syntax_use_cmd_abv = "0"
  and re-sourcing the syntax file:
    runtime pt.vim (or dctl.vim, or dctlpt.vim)
  or you can put it in your .vimrc.

  Although it all works, you might think twice before using command
  abbreviations in real code.  Synopsys could come along later and add a
  command that makes your abbreviation invalid (this has nothing to do with
  syntax highlighting, it is just a risk involved in using abbreviations).
  Also, enabling command abbreviations will slow down vim.

  The method of detecting a command has been fundamentally altered.  This 
  fixes some wierd corner-cases where strings would highlight outside of a
  command context.  As part of this, the files have been completely 
  de-coupled from the standard tcl.vim file.  It is no longer sourced.
  Please let me know if I broke something.

  To make eval, uplevel, and &cmd (see my paper "My Favorite DC/PT tcl tricks" 
  for details) work, I had to add some code that might impact performance.  
  The operation is controlled by switches, which default on.

  If you want to turn off eval, uplevel, and &cmd syntaxing, add this to your 
  .vimrc:
    let fm_syntax_enable_eval = "0"
    let pt_syntax_enable_eval = "0"
    let dctl_syntax_enable_eval = "0"
    let dctlpt_syntax_enable_eval = "0"

  Each file has its own switch because you might want to enable it for pt
  but not for dctl or dctlpt (because the latter two are much bigger files).

  If you're using &cmd, also get the latest mk_tcl_syntax.pl (it's in all the
  tarballs now) and use the -eval switch:
    mk_tcl_syntax -eval '&cmd'

  You might have to escape the "&", depending on the context you're calling it
  from.

Send comments and suggestions to:

paulzimmer@zimmerdesignservices.com


Paul Zimmer 
17 September, 2008
