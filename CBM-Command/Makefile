CC65_TARGET = c64
CC65_HOME = /usr

TITLE = cbmcommand
CONFIG_TITLE = cbmcmdcfg

PROGRAM = $(TITLE).$(CC65_TARGET)
CONFIG_PROGRAM = $(CONFIG_TITLE).$(CC65_TARGET)

CC = $(CC65_HOME)/bin/cc65
CA = $(CC65_HOME)/bin/ca65
LD = $(CC65_HOME)/bin/ld65
CL = $(CC65_HOME)/bin/cl65
AR = $(CC65_HOME)/bin/ar65
AS = $(CC65_HOME)/bin/as65

CFLAGS = -t $(CC65_TARGET) -Or -Wl -D,__STACKSIZE__=$$400
AFLAGS = -t $(CC65_TARGET) 

LDFLAGS = -t $(CC65_TARGET) -m $(PROGRAM).map

VIC_CONFIG := vic20-32k.cfg

ifeq "$(T)" "vic20"
LD_CONFIG := -C $(VIC_CONFIG)
endif

LIBS := $(CC65_TARGET)/cc65.lib

CONFIG_SOURCES = \
	Configuration.c \
	ConfigurationManager.c \
	globalInput.c \
	globals.c \
	screen.c

SOURCES	= \
	CBM-Command.c \
	CBM-REL.c \
	Configuration.c \
	DriveMenu.c \
	drives.c \
	FileMenu.c \
	globalInput.c \
	globals.c \
	input.c \
	menus.c \
	screen.c \
	Viewer.c \
	builtStamp.c

ASMFILES := \
	res/common/driveRoutines.s \
	res/$(CC65_TARGET)/PlatformSpecific.s \
	cc65/common/_printf.s \
	${wildcard cc65/$(CC65_TARGET)/systime.s}
# _systime() is a library function; but, it's called by only another library
# function -- in only some of the programs.  Therefore, its substitute
# must be linked directly.  [If it was in cc65.lib, then ld65 would pass by it
# before time() asked for it.]

# These functions are called by the programs' files.
# Their substitutes can be put into a library.
LIBFILES := \
	cc65/common/strcat.s \
	cc65/cbm/cbm_dir.c \
	cc65/cbm/cbm_open.s \
	cc65/cbm/cclear.s \
	cc65/cbm/chline.s \
	cc65/cbm/cvline.s \
	${wildcard cc65/$(CC65_TARGET)/clrscr.s} \
	cc65/$(CC65_TARGET)/cputc.s \
	cc65/conio/cputs.s \
	cc65/conio/callgoto.s

%.o: %.c
	$(CL) -c $(CFLAGS) -o $@ $<

%.o: %.s
	$(CA) $(AFLAGS) -o $@ $<

.PHONY: all
ifeq "$(CC65_TARGET)" "pet"
all: pet/$(PROGRAM)40 pet/$(PROGRAM)80 pet/$(CONFIG_PROGRAM)40 pet/$(CONFIG_PROGRAM)80
else
all: $(CC65_TARGET) $(CC65_TARGET)/$(PROGRAM) $(CC65_TARGET)/$(CONFIG_PROGRAM)
endif

$(CC65_TARGET):
	mkdir -p $(CC65_TARGET)

$(CC65_TARGET)/$(PROGRAM): $(SOURCES:.c=.o) $(ASMFILES:.s=.o) $(LIBS)
	$(CL) $(LDFLAGS) -o $@ $^

$(CC65_TARGET)/$(CONFIG_PROGRAM): $(CONFIG_SOURCES:.c=.o) res/$(CC65_TARGET)/PlatformSpecific.o $(LIBS)
	$(CL) $(LDFLAGS) -o $@ $+

# Make this target only do the linking?
$(LIBS): $(LIBFILES)
	$(CL) $(LDFLAGS) -c $?
	$(AR) a $@ $(addsuffix .o,${basename $?})

# Our cleaning targets, for managing the project
.PHONY: mostly-clean clean zap
mostly-clean:
		$(RM) $(SOURCES:.c=.o) $(SOURCES:.c=.s)
		$(RM) $(CONFIG_SOURCES:.c=.o) $(CONFIG_SOURCES:.c=.s)
		$(RM) $(ASMFILES:.s=.o)
		$(RM) $(addsuffix .o,${basename $(LIBFILES)}) $(LIBS)

clean: mostly-clean
ifeq "$(CC65_TARGET)" "pet"
		$(RM) ${wildcard pet/$(TITLE).pet* pet/$(CONFIG_TITLE).pet*}
else
		$(RM) $(CC65_TARGET)/$(PROGRAM) $(CC65_TARGET)/$(CONFIG_PROGRAM)
endif

zap: clean
	$(RM) $(TITLE).d64 $(TITLE).d80
