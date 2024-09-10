NAME=embedcl
CPPSRC:=$(wildcard *.cpp)

ifeq ($(PLATFORM),Darwin)
SIGNING_IDENTITY=$(CODER)
endif

ifeq ($(PLATFORM),Darwin)
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

ifeq ($(PLATFORM),Darwin)
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
