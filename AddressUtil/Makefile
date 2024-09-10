NAME=addressutil
SRC=$(wildcard *.cpp)
##
## Detect Operating System
##

UNAME                   := $(shell uname -s)

# we need to strip the windows version number to be able to build enigma on cygwin hosts
UNAME                   := $(patsubst CYGWIN_NT-%,CYGWIN,$(UNAME))

# same for msys
UNAME                   := $(patsubst MSYS_NT-%,MSYS2,$(UNAME))
UNAME                   := $(patsubst MINGW32_NT-%,MSYS2,$(UNAME))
UNAME                   := $(patsubst MINGW64_NT-%,MSYS2,$(UNAME))

ifeq (,$(filter $(UNAME),Linux Darwin CYGWIN MSYS2))
$(error "! Your Operating System ($(UNAME)) is not supported by this Makefile")
endif

ifeq ($(UNAME),Darwin)
SIGNING_IDENTITY="Apple Development: sonar@gmx.com (XXXXXXXXXX)"
endif

ifeq ($(UNAME),Darwin)
.PHONY: all sign0 sign1 sign2 install uninstall clean version

all: x86_app sign0 arm_app sign1 universal_app sign2 version

x86_app: $(SRC}
	for file in ${SRC} ; do\
		${CXX} -c $$file ${INCLUDE} ${CXXFLAGS};\
	done
	mkdir -p ${LIBDIR}
	${CXX} ${LDFLAGS} -dynamiclib -undefined suppress -flat_namespace *.o -o ${LIBDIR}/lib$(NAME)-x86_64.dylib -target x86_64-apple-macos10.12

arm_app: ${SRC}
	for file in ${SRC} ; do\
		${CXX} -c $$file ${INCLUDE} ${CXXFLAGS};\
	done
	mkdir -p ${LIBDIR}
	${CXX} ${LDFLAGS} -dynamiclib -undefined suppress -flat_namespace *.o -o ${LIBDIR}/lib$(NAME)-arm64.dylib -target arm64-apple-macos11

universal_app: ${LIBDIR}/lib$(NAME)-x86_64.dylib ${LIBDIR}/lib$(NAME)-arm64.dylib
	lipo -create -output ${LIBDIR}/lib$(NAME).dylib ${LIBDIR}/lib$(NAME)-x86_64.dylib ${LIBDIR}/lib$(NAME)-arm64.dylib
endif

ifeq ($(UNAME),Darwin)
sign0:
	export CODESIGN_ALLOCATE=/Applications/Xcode.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/../bin/codesign_allocate
	xcrun codesign -f -s $(SIGNING_IDENTITY) --preserve-metadata=identifier,entitlements --timestamp=none ${LIBDIR}/lib$(NAME)-x86_64.dylib
	
sign1:
	export CODESIGN_ALLOCATE=/Applications/Xcode.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/../bin/codesign_allocate
	xcrun codesign -f -s $(SIGNING_IDENTITY) --preserve-metadata=identifier,entitlements --timestamp=none ${LIBDIR}/lib$(NAME)-arm64.dylib
	
sign2:
	export CODESIGN_ALLOCATE=/Applications/Xcode.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/../bin/codesign_allocate
	xcrun codesign -f -s $(SIGNING_IDENTITY) --preserve-metadata=identifier,entitlements --timestamp=none ${LIBDIR}/lib$(NAME).dylib
	
endif

clean:
	rm -rf *.a
	rm -rf *.o
	rm -rf *.gcno
	rm -rf *.gcda
