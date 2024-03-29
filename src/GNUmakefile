
# GNUmakefile for shader_scene

# use VERBOSE=1   to see the build commands
# use CFG=release to build release
# use CFG=debug   to build debug

VPATH = ../stb ../glew/src

CFG ?= debug

TARGET = shader_scene

UNAME := $(shell uname)

SHADER_SCENE_C = \
  glew.c \
  request_vsync.c \
  $(NULL)

SHADER_SCENE_CPP = \
  inverse.cpp \
  normalize.cpp \
  sqrt.cpp \
  glmatrix.cpp \
  main.cpp \
  matrix_stack.cpp \
  menus.cpp \
  scene.cpp \
  texture.cpp \
  trackball.cpp \
  stb_image.cpp \
  $(NULL)

SHADER_SCENE_OBJS = \
     $(patsubst %.cpp, objs.$(CFG)$(APP_PLATFORM)/%.o, $(SHADER_SCENE_CPP)) \
     $(patsubst %.c,   objs.$(CFG)$(APP_PLATFORM)/%.o, $(SHADER_SCENE_C)) \
     $(NULL)

SHADER_SCENE_DEPS = \
     $(patsubst %.cpp, deps.$(CFG)$(APP_PLATFORM)/%.d, $(SHADER_SCENE_CPP)) \
     $(patsubst %.c,   deps.$(CFG)$(APP_PLATFORM)/%.d, $(SHADER_SCENE_C)) \
     $(NULL)

OBJS = $(SHADER_SCENE_OBJS)

ifeq ($(CFG),debug)
DEBUG_OPT = -g -DSK_DEBUG
else
OPT_OPT = -O2 -DNDEBUG -fno-strict-aliasing
endif

CC         = gcc
CXX        = g++
#CC         = gcc-4.1
#CXX        = g++-4.1
CFLAGS     += -Wall -Wstrict-aliasing=1 -Wno-sign-compare $(DEBUG_OPT) $(OPT_OPT)
CXXFLAGS   += -Wall -Wno-strict-aliasing -Wno-sign-compare $(DEBUG_OPT) $(OPT_OPT)
CFLAGS     += -DGLEW_STATIC
CFLAGS     += -I.
CXXFLAGS   += -I.
CFLAGS     += -Iinclude
CXXFLAGS   += -Iinclude
CFLAGS     += -I"$(BOOST)"
CXXFLAGS   += -I"$(BOOST)"
CXXFLAGS   += -DGLEW_STATIC
CFLAGS     += -I../glew/include
CXXFLAGS   += -I../glew/include

DEPEND_FILES = $(OBJS:%.o=%.d)
DEPEND_OPTS = -MMD

ifeq ($(UNAME), Darwin)
  CLINKFLAGS += -L/Developer/SDKs/MacOSX10.5.sdk/usr/X11/lib
  CLINKFLAGS += -framework GLUT
  CLINKFLAGS += -framework OpenGL
  CXXFLAGS   += -I/usr/X11/include
  CLINKFLAGS += -Wl,-dylib_file,/System/Library/Frameworks/OpenGL.framework/Versions/A/Libraries/libGL.dylib:/System/Library/Frameworks/OpenGL.framework/Versions/A/Libraries/libGL.dylib
  CFLAGS     += $(DEPEND_OPTS)
  CXXFLAGS   += $(DEPEND_OPTS)
else
  ifeq ($(findstring CYGWIN, $(UNAME)), CYGWIN)
    CFLAGS     += -D_WIN32
    CXXFLAGS   += -D_WIN32
    CFLAGS     += -DGLUT_DISABLE_ATEXIT_HACK
    CXXFLAGS   += -DGLUT_DISABLE_ATEXIT_HACK

    # XXX assume Visual Studio built Release/cg4cpp.lib
    CLINKFLAGS += -LRelease

    CLINKFLAGS += -lglut32
    CLINKFLAGS += -lglu32 -lopengl32 -lm
    EXE = .exe
  else
    ifeq ($(UNAME), SunOS)
      CLINKFLAGS += -L/usr/X11R6/lib64 -L/usr/X11R6/lib
      CLINKFLAGS += -lglut -lXi -lXmu -lX11 -lm
      CLINKFLAGS += -lGLU -lGL
      CLINKFLAGS += -lpthread
      BOOST   := /usr/include/boost
    else
      BOOST   := /usr/include/boost
      APP_PLATFORM ?= 64
      ifeq ($(APP_PLATFORM),32)
        APP_PLATFORM_OPT = -m32
      else
        APP_PLATFORM_OPT = -m64
      endif
      CFLAGS     += -DGLUT_DISABLE_ATEXIT_HACK
      CXXFLAGS   += -DGLUT_DISABLE_ATEXIT_HACK
      CFLAGS     += $(APP_PLATFORM_OPT)
      CXXFLAGS   += $(APP_PLATFORM_OPT)
      CLINKFLAGS += $(APP_PLATFORM_OPT)
      # freeglut
      CLINKFLAGS += -L$(wildcard /usr/lib/nvidia-340-updates /usr/lib/nvidia-340)
      CLINKFLAGS += -lglut
      CLINKFLAGS += -lXi -lXmu -lX11 -lm
      CLINKFLAGS += -lGLU -lGL
      CLINKFLAGS += -lpthread
      CFLAGS     += $(DEPEND_OPTS)
      CXXFLAGS   += $(DEPEND_OPTS)
    endif
  endif
endif

BINARY := $(TARGET:=$(EXE))

.PHONY: all run clean clobber inform both release debug rrun drun

all: bin.$(CFG)$(APP_PLATFORM)/$(BINARY)

both: release debug

release:
	$(MAKE) CFG=release

debug:
	$(MAKE) CFG=debug

run: bin.$(CFG)$(APP_PLATFORM)/$(BINARY)
	./bin.$(CFG)$(APP_PLATFORM)/$(BINARY) $(ARGS)

rrun:
	$(MAKE) CFG=release run

drun:
	$(MAKE) CFG=debug run

gdb: bin.$(CFG)$(APP_PLATFORM)/$(BINARY)
	gdb ./bin.$(CFG)$(APP_PLATFORM)/$(BINARY)

clean:
	$(RM) $(BINARY) $(OBJS) $(DEPEND_FILES)

clobber: clean
	$(RM) *.bak *.o *~ $(DEPEND_FILES)
	$(RM) -rf bin*.debug objs.debug
	$(RM) -rf bin*.release objs.release

inform:
ifneq ($(CFG),release)
ifneq ($(CFG),debug)
	@echo "Invalid configuration "$(CFG)" specified"
	@echo "Possible choices for configuration are 'release' and 'debug'"
	@exit 1
endif
endif
	@echo "Configuration "$(CFG)
ifdef APP_PLATFORM
	@echo "Platform      "$(APP_PLATFORM)
endif
	@echo "--------------------------"

### implicit rules

bin.$(CFG)$(APP_PLATFORM)/$(BINARY): $(OBJS) | GNUmakefile inform $(LIBRARIES_TO_BUILD)
	@mkdir -p $(dir $@)
ifndef VERBOSE
	@echo Linking $@...
endif
	$(CXX) -g -o $@ $^ $(CLINKFLAGS)

objs.$(CFG)$(APP_PLATFORM)/%.o : %.cpp | inform
	@mkdir -p $(dir $@)
ifndef VERBOSE
	@echo Compiling $(notdir $@)
endif
	$(CXX) -c $(CXXFLAGS) -o $@ $<

objs.$(CFG)$(APP_PLATFORM)/%.o : %.c | inform
	@mkdir -p $(dir $@)
ifndef VERBOSE
	@echo Compiling $(notdir $@)
endif
	$(CC) -c $(CXXFLAGS) -o $@ $<

ifneq ($(MAKECMDGOALS),clean)
ifneq ($(MAKECMDGOALS),clobber)
sinclude $(DEPEND_FILES)
endif
endif

ifndef VERBOSE
.SILENT:
endif
