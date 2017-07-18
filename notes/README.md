Taking notes from www.oreilly.com/openbook/make3/book

Typically the default goal in most makefiles is to build a program.

make is flexible enough to be used anywhere "one kind of file depends on another" from traditional programming in C/C++ to java, TEX, database management, and more.

The first rule seen by make is used as the default rule.

A rule consists of three parts: the target, its prerequisites, and the commands to perform:

	target: prereq1, prereq2
		commands

Makefiles are usually structured top-down.
Such as
  all
  ...
  clean

comment character for make is the hash or pound sign, #.

There are a number of different kinds of rules.

- Explicit rules
- Pattern rules (wildcards or globbing)
- Implicit rules (either pattern rules or suffix fules in database)
-   Static pattern rules
-   Suffix rules (obsolete, replaced by pattern rules)


wildcards:

~ : home
* : one or more
? : one
[...] : character class
[^...] : opposite character class


Phony Targets:

Targets that do not represent files are known as phony targets.
Normally, phony targets will always be executed.

Ex.

.PHONY clean
clean:
	rm -f *.o lexer.c

always execute clean if a file named clean exists.


Standard phony targets:

  all
  install
  clean
  distclean
  TAGS - not really a phony, included because it's the only standard non-phony target we know of
  info
  check


Empty Targets: (cookie)

not easy to understand


Variables:

$(variable-name)


Automatic Variables:

$@ : filename representing the target
$% : filename element of an archive member specification
$< : filename of the first prerequisite
$? : names of all prerequisites that are newer than the target, separated by spaces.
$^ : filenames of all the prerequisites
$+ : similar to $^ but includes duplicates
$* : stem of the target filename. A stem is typically a filename without its suffix.

return only directory portion of the value:
Ex:
  $(@D), $(<D), ...

return only file portion of the value:
Ex:
  $(@F), $(<F), ...


A pattern rule looks like the normal rules you have already seen except the stem of the file (the portion before the suffix) is represented by a % character.

Ex:
  %.o: %.c
    $(COMPILE.c) $(OUTPUT_OPTION) $<

there is a special rule to generate a file with no suffix (always an executable) from a .c file:

  %: %.c
    $(LINK.c) $^ $(LOADLIBES) $(LDLIBS) -o $@


The only difference between this rule and an ordinary pattern rule is the initial
$(OBJECTS): specification. This limits the rule to the files listed in the $(OBJECTS) variable.
Static Pattern Rules:    
Ex:
  $(OBJECTS): %.o: %c
    $(CC) -c $(CFLAGS) $< -o $@


Suffix Rules:
Suffix rules are the original (and obsolete) way of defining implicit rules. 

Suffix rules consist of one or two suffixes concatenated and used as a target:
.c.o:
 $(COMPILE.c) $(OUTPUT_OPTION) $<
This is a little confusing because the prerequisite suffix comes first and the target suffix
second. This rule matches the same set of targets and prerequisites as:
%.o: %.c
 $(COMPILE.c) $(OUTPUT_OPTION) $<
The suffix rule forms the stem of the file by removing the target suffix. It forms the
prerequisite by replacing the target suffix with the prerequisite suffix. The suffix rule
is recognized by make only if the two suffixes are in a list of known suffixes.
The above suffix rule is known as a double-suffix rule since it contains two suffixes.
There are also single-suffix rules. As you might imagine a single-suffix rule contains
only one suffix, the suffix of the source file. These rules are used to create executables
since Unix executables do not have a suffix:
.p:
 $(LINK.p) $^ $(LOADLIBES) $(LDLIBS) -o $@
This rule produces an executable image from a Pascal source file. This is completely
analogous to the pattern rule:
%: %.p
 $(LINK.p) $^ $(LOADLIBES) $(LDLIBS) -o $@
The known suffix list is the strangest part of the syntax. A special target, .SUFFIXES,
is used to set the list of known suffixes. Here is the first part of the default .SUFFIXES
definition:
.SUFFIXES: .out .a .ln .o .c .cc .C .cpp .p .f .F .r .y .l
You can add your own suffixes by simply adding a .SUFFIXES rule to your makefile:
.SUFFIXES: .pdf .fo .html .xml
If you want to delete all the known suffixes (because they are interfering with your
special suffixes) simply specify no prerequisites:
.SUFFIXES: 
