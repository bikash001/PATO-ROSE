## A sample Makefile to build a ROSE tool.
##
## Important: remember that Makefile recipes must contain tabs:
##
##     <target>: [ <dependency > ]*
##         [ <TAB> <command> <endl> ]+
## So you have to replace spaces with Tabs if you copy&paste this file from a browser!

LEVEL=../..

include $(LEVEL)/Makefile.common

## Configure
CC=g++
CCFLAGS = -I$(BOOST_INS)/include -I$(ROSE_INS)/include/rose -g
LIBS = -L$(ROSE_INS)/lib -lrose -L$(BOOST_INS)/lib -lboost_iostreams -lboost_system

Prolog = swipl
#PLFLAGS = `pkg-config --cflags --libs $(Prolog)` -DSWIPL -DREADLINE 
#FCC = swipl-ld 

## Your translator
TRANSLATOR = rosePrgKnowledgeBuilder
TRANSLATOR_EXE = $(TRANSLATOR).exe
DEP = $(TRANSLATOR).o 

#-------------------------------------------------------------
# Makefile Targets
#-------------------------------------------------------------

all : $(TRANSLATOR).exe

# compile the translator and generate an executable
# -g is recommended to be used by default to enable debugging your code
# Note: depending on the version of boost, you may have to use something like -I $(BOOST_ROOT)/include/boost-1_40 instead. 
$(TRANSLATOR).exe : $(TRANSLATOR).cpp
	$(CC) -o $@ $^ $(CCFLAGS) $(LIBS) $(PLFLAGS)

# test the translator
TESTDIR = test/
TESTCODE = test.c

# promote all C files to be cpp files
TEST_CODES_REQUIRED_TO_PASS = $(addprefix $(TESTDIR), ut.cpp is.cpp)

TEST_CODES_OWL_RESULTS = $(TEST_CODES_REQUIRED_TO_PASS:.cpp=.cpp.owl) # change .c to .c.owl
TEST_CODES_TTL_RESULTS = $(TEST_CODES_REQUIRED_TO_PASS:.cpp=.cpp.ttl) 
TEST_CODES_PL_RESULTS = $(TEST_CODES_REQUIRED_TO_PASS:.cpp=.cpp.pl) 

# each .c.owl file depends on the corresponding .c file
$(TEST_CODES_OWL_RESULTS): %.cpp.owl:%.cpp $(TRANSLATOR).exe
	./$(TRANSLATOR).exe -emit-owl $@ -c $< 

$(TEST_CODES_TTL_RESULTS): %.cpp.ttl:%.cpp $(TRANSLATOR).exe
	./$(TRANSLATOR).exe -emit-ttl $@ -c $< 

$(TEST_CODES_PL_RESULTS): %.cpp.pl:%.cpp $(TRANSLATOR).exe
	./$(TRANSLATOR).exe -emit-pl $@ -c $< 

check: $(TEST_CODES_OWL_RESULTS) $(TEST_CODES_TTL_RESULTS) $(TEST_CODES_PL_RESULTS)

check-alive:
	./$(TRANSLATOR).exe -alive true -c $(TESTDIR)$(TESTCODE)

clean:
	rm -rf $(TRANSLATOR_EXE) *.exe *.o $(TESTDIR)*.owl $(TESTDIR)*.pl $(TESTDIR)*.ttl

