# $Id: Makefile 25724 2012-07-24 23:35:36Z bangerth $


# For the small projects Makefile, you basically need to fill in only
# four fields.
#
# The first is the name of the application. It is assumed that the
# application name is the same as the base file name of the single C++
# file from which the application is generated.
target = step-4

# The second field determines whether you want to run your program in
# debug or optimized mode. The latter is significantly faster, but no
# run-time checking of parameters and internal states is performed, so
# you should set this value to `on' while you develop your program,
# and to `off' when running production computations.
debug-mode = on


# As third field, we need to give the path to the top-level deal.II
# directory. You need to adjust this to your needs. Since this path is
# probably the most often needed one in the Makefile internals, it is
# designated by a single-character variable, since that can be
# reference using $D only, i.e. without the parentheses that are
# required for most other parameters, as e.g. in $(target).
D = $(DEALII_HOME)


# The last field specifies the names of data and other files that
# shall be deleted when calling `make clean'. Object and backup files,
# executables and the like are removed anyway. Here, we give a list of
# files in the various output formats that deal.II supports.
clean-up-files = *gmv *gnuplot *gpl *eps *pov *vtk *ucd *.d2




#
#
# Usually, you will not need to change anything beyond this point.
#
#
# The next statement tells the `make' program where to find the
# deal.II top level directory and to include the file with the global
# settings
include $D/common/Make.global_options


# Since the whole project consists of only one file, we need not
# consider difficult dependencies. We only have to declare the
# libraries which we want to link to the object file. deal.II has two
# libraries: one for the debug mode version of the
# application and one for optimized mode.
libs.g   := $(lib-deal2.g)
libs.o   := $(lib-deal2.o)


# We now use the variable defined above to switch between debug and
# optimized mode to select the set of libraries to link with. Included
# in the list of libraries is the name of the object file which we
# will produce from the single C++ file. Note that by default we use
# the extension .g.o for object files compiled in debug mode and .o for
# object files in optimized mode (or whatever local default on your
# system is instead of .o)
ifeq ($(debug-mode),on)
  libraries = $(target).g.$(OBJEXT) $(libs.g)
else
  libraries = $(target).$(OBJEXT) $(libs.o)
endif


# Now comes the first production rule: how to link the single object
# file produced from the single C++ file into the executable. Since
# this is the first rule in the Makefile, it is the one `make' selects
# if you call it without arguments.
all: $(target)$(EXEEXT)
$(target)$(EXEEXT) : $(libraries)
	@echo ============================ Linking $@
	@$(CXX) -o $@ $^ $(LIBS) $(LDFLAGS)


# To make running the application somewhat independent of the actual
# program name, we usually declare a rule `run' which simply runs the
# program. You can then run it by typing `make run'. This is also
# useful if you want to call the executable with arguments which do
# not change frequently. You may then want to add them to the
# following rule:
run: $(target)$(EXEEXT)
	@echo ============================ Running $<
	@./$(target)$(EXEEXT)


# As a last rule to the `make' program, we define what to do when
# cleaning up a directory. This usually involves deleting object files
# and other automatically created files such as the executable itself,
# backup files, and data files. Since the latter are not usually quite
# diverse, you needed to declare them at the top of this file.
clean:
	-rm -f *.$(OBJEXT) *~ Makefile.dep $(target)$(EXEEXT) $(clean-up-files)


# Since we have not yet stated how to make an object file from a C++
# file, we should do so now. Since the many flags passed to the
# compiler are usually not of much interest, we suppress the actual
# command line using the `at' sign in the first column of the rules
# and write the string indicating what we do instead.
./%.g.$(OBJEXT) :
	@echo "==============debug========= $(<F)  ->  $@"
	@$(CXX) $(CXXFLAGS.g) -c $< -o $@
./%.$(OBJEXT) :
	@echo "==============optimized===== $(<F)  ->  $@"
	@$(CXX) $(CXXFLAGS.o) -c $< -o $@


# The following statement tells make that the rules `run' and `clean'
# are not expected to produce files of the same name as Makefile rules
# usually do.
.PHONY: all run clean


# Finally there is a rule which you normally need not care much about:
# since the executable depends on some include files from the library,
# besides the C++ application file of course, it is necessary to
# re-generate the executable when one of the files it depends on has
# changed. The following rule creates a dependency file
# `Makefile.dep', which `make' uses to determine when to regenerate
# the executable. This file is automagically remade whenever needed,
# i.e. whenever one of the cc-/h-files changed. Make detects whether
# to remake this file upon inclusion at the bottom of this file.
#
# If the creation of Makefile.dep fails, blow it away and fail
Makefile.dep: $(target).cc Makefile \
              $(shell echo $D/include/deal.II/*/*.h)
	@echo ============================ Remaking $@
	@$D/common/scripts/make_dependencies  $(INCLUDE) -B. $(target).cc \
		> $@ \
	  || (rm -f $@ ; false)
	@if test -s $@ ; then true ; else rm $@ ; false ; fi

# To make the dependencies known to `make', we finally have to include
# them:
include Makefile.dep


