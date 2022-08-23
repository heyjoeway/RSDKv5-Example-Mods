.DEFAULT_GOAL := all

PKGCONFIG	=  pkg-config
STRIP		?= strip

STATIC		?= 1
DEBUG		?= 1
VERBOSE		?= 0
PROFILE		?= 0

MOD_NAME   ?= ManiaTouchControls
MOD_SUFFIX ?= .so
MOD_ALLC   ?= 1

MOD_CFLAGS  =
MOD_LDFLAGS = -shared
MOD_LIBS    =

MOD_PREBUILD =
MOD_PRELINK  =
MOD_POSTLINK =

DEFINES      =

# =============================================================================
# Detect default platform if not explicitly specified
# =============================================================================

ifeq ($(OS),Windows_NT)
	PLATFORM ?= Windows
else
	UNAME_S := $(shell uname -s)

	ifeq ($(UNAME_S),Linux)
		PLATFORM ?= Linux
	endif

	ifeq ($(UNAME_S),Darwin)
		PLATFORM ?= macOS
	endif

endif

PLATFORM ?= Unknown

# =============================================================================

ifneq ("$(wildcard makefiles/$(PLATFORM).cfg)","")
	include makefiles/$(PLATFORM).cfg
endif

OUTDIR = bin/$(PLATFORM)
MOD_OBJDIR = obj/$(PLATFORM)/$(MOD_NAME)


# =============================================================================

CFLAGS ?= $(CXXFLAGS)
DEFINES += -DBASE_PATH='"$(BASE_PATH)"'

ifeq ($(DEBUG),1)
	CXXFLAGS += -g
	CFLAGS += -g
	STRIP = :
else
	CXXFLAGS += -O3
	CFLAGS += -O3
endif

ifeq ($(PROFILE),1)
	CXXFLAGS += -pg -g -fno-inline-functions -fno-inline-functions-called-once -fno-optimize-sibling-calls -fno-default-inline
	CFLAGS += -pg -g -fno-inline-functions -fno-inline-functions-called-once -fno-optimize-sibling-calls -fno-default-inline
endif

ifeq ($(VERBOSE),0)
	CC := @$(CC)
	CXX := @$(CXX)
endif

CFLAGS_ALL += $(CFLAGS) \
			   -fsigned-char 
		
CXXFLAGS_ALL += $(CXXFLAGS) \
			   -std=c++17 \
			   -fsigned-char \
			   -fpermissive 

LDFLAGS_ALL = $(LDFLAGS)

MOD_INCLUDES = \
	-I./$(MOD_NAME)/   		\
	-I./$(MOD_NAME)/Objects/ \
	-I./GameAPI/C/

MOD_SOURCES = \
    GameAPI/C/GameAPI/Game \
    $(MOD_NAME)/dllmain \
    $(MOD_NAME)/Helpers \
    $(MOD_NAME)/Objects/All

$(shell mkdir -p $(OUTDIR))

MOD_OBJECTS += $(addprefix $(MOD_OBJDIR)/, $(addsuffix .o, $(MOD_SOURCES)))
MOD_PATH = $(OUTDIR)/$(MOD_NAME)$(MOD_SUFFIX)
$(shell mkdir -p $(MOD_OBJDIR))

$(MOD_OBJDIR)/%.o: $(MOD_PREBUILD) %.c
	@mkdir -p $(@D)
	@echo compiling $<...
	$(CC) -c -fPIC $(CFLAGS_ALL) $(MOD_FLAGS) $(MOD_INCLUDES) $(DEFINES) $< -o $@
	@echo done $<

$(MOD_PATH): $(MOD_PRELINK) $(MOD_OBJECTS)
	@echo linking game...
	$(CXX) $(CXXFLAGS_ALL) $(LDFLAGS_ALL) $(MOD_LDFLAGS) $(MOD_OBJECTS) $(MOD_LIBS) -o $@ 
 	$(STRIP) $@
	@echo done linking game

all: $(MOD_POSTLINK) $(MOD_PATH)

clean:
	rm -rf $(MOD_OBJDIR) && rm -rf $(MOD_PATH)
