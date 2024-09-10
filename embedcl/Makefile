NAME=embedcl
CPPSRC:=$(wildcard *.cpp)
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
.PHONY: all sign0 sign1 sign2 install uninstall clean

all: x86_app sign0 arm_app sign1 universal_app sign2 

x86_app: ${CPPSRC}
	${CXX} -o ${NAME}-x86_64.bin ${CPPSRC} ${INCLUDE} ${CXXFLAGS} -target x86_64-apple-macos10.12
	mkdir -p $(BINDIR)

arm_app: ${CPPSRC}
	${CXX} -o ${NAME}-arm64.bin ${CPPSRC} ${INCLUDE} ${CXXFLAGS} -target arm64-apple-macos11
	mkdir -p $(BINDIR)

universal_app: ${NAME}-x86_64.bin ${NAME}-arm64.bin
	lipo -create -output ${NAME} ${NAME}-x86_64.bin ${NAME}-arm64.bin
	cp ${NAME} $(BINDIR)/${NAME}
endif

ifeq ($(UNAME),Darwin)
sign0:
	export CODESIGN_ALLOCATE=/Applications/Xcode.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/../bin/codesign_allocate
	xcrun codesign -f -s $(SIGNING_IDENTITY) --preserve-metadata=identifier,entitlements --timestamp=none ${NAME}-x86_64.bin
	
sign1:
	export CODESIGN_ALLOCATE=/Applications/Xcode.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/../bin/codesign_allocate
	xcrun codesign -f -s $(SIGNING_IDENTITY) --preserve-metadata=identifier,entitlements --timestamp=none ${NAME}-arm64.bin
	
sign2:
	export CODESIGN_ALLOCATE=/Applications/Xcode.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/../bin/codesign_allocate
	xcrun codesign -f -s $(SIGNING_IDENTITY) --preserve-metadata=identifier,entitlements --timestamp=none ${NAME}
	
endif

clean:
	rm -rf *.gcno
	rm -rf *.gcda
	rm -rf ${NAME}
ifeq ($(PLATFORM),Darwin)
ifeq ($(BUILD_DEBUG),1)
	rm -rf ${NAME}.dSYM
endif
endif
