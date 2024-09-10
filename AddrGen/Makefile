CPPSRC:=$(wildcard *.cpp)

ifeq ($(PLATFORM),Darwin)
SIGNING_IDENTITY=$(CODER)
endif

ifeq ($(PLATFORM),Darwin)
.PHONY: all sign0 sign1 sign2 install uninstall clean

all: x86_app sign0 arm_app sign1 universal_app sign2 

x86_app: ${CPPSRC}
	${CXX} -o addrgen-x86_64.bin ${CPPSRC} ${INCLUDE} -I${CUDA_INCLUDE} ${CXXFLAGS} ${LIBS} -laddressutil -lsecp256k1 -lcryptoutil -lsecp256k1 -lcommonutils -lcmdparse -target x86_64-apple-macos10.12
	mkdir -p $(BINDIR)

arm_app: ${CPPSRC}
	${CXX} -o addrgen-arm64.bin ${CPPSRC} ${INCLUDE} -I${CUDA_INCLUDE} ${CXXFLAGS} ${LIBS} -laddressutil -lsecp256k1 -lcryptoutil -lsecp256k1 -lcommonutils -lcmdparse -target arm64-apple-macos11
	mkdir -p $(BINDIR)

universal_app: addrgen-x86_64.bin addrgen-arm64.bin
	lipo -create -output addrgen.bin addrgen-x86_64.bin addrgen-arm64.bin
	cp addrgen.bin $(BINDIR)/addrgen
endif

ifeq ($(PLATFORM),Darwin)
sign0:
	export CODESIGN_ALLOCATE=/Applications/Xcode.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/../bin/codesign_allocate
	xcrun codesign -f -s $(SIGNING_IDENTITY) --preserve-metadata=identifier,entitlements --timestamp=none addrgen-x86_64.bin
	
sign1:
	export CODESIGN_ALLOCATE=/Applications/Xcode.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/../bin/codesign_allocate
	xcrun codesign -f -s $(SIGNING_IDENTITY) --preserve-metadata=identifier,entitlements --timestamp=none addrgen-arm64.bin
	
sign2:
	export CODESIGN_ALLOCATE=/Applications/Xcode.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/../bin/codesign_allocate
	xcrun codesign -f -s $(SIGNING_IDENTITY) --preserve-metadata=identifier,entitlements --timestamp=none addrgen.bin
	
endif

clean:
	rm -rf *.gcno
	rm -rf *.gcda
	rm -rf addrgen.bin
ifeq ($(PLATFORM),Darwin)
ifeq ($(BUILD_DEBUG),1)
	rm -rf addrgen.bin.dSYM
endif
endif
