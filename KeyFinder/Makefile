CPPSRC:=$(wildcard *.cpp)

ifeq ($(PLATFORM),Darwin)
SIGNING_IDENTITY=$(CODER)
endif

ifeq ($(PLATFORM),Darwin)
.PHONY: all sign0 sign1 sign2 install uninstall clean

all: x86_app sign0 arm_app sign1 universal_app sign2

x86_app: ${CPPSRC}
ifeq ($(BUILD_CUDA), 1)
ifeq ($(PLATFORM),Darwin)
	${CXX} -DBUILD_CUDA -o cuKeyFinder-x86_64.bin ${CPPSRC} ${INCLUDE} -I${CUDA_INCLUDE} ${CXXFLAGS} ${LIBS} ${OPENCL_LIB} -llogger -lcommonutils -lcmdparse -lsecp256k1 -lcryptoutil -lkeyfinder -laddressutil -lclutil -lclutil -lcudautil -lCudaKeySearchDevice -lcudadevrt -lcudart -target x86_64-apple-macos10.12
else
	${CXX} -DBUILD_CUDA -o cuKeyFinder-x86_64.bin ${CPPSRC} ${INCLUDE} -I${CUDA_INCLUDE} ${CXXFLAGS} ${LIBS} -Wl,--start-group ${OPENCL_LIB} -llogger -lcommonutils -lcmdparse -lsecp256k1 -lcryptoutil -lkeyfinder -laddressutil -lclutil -lclutil -lcudautil -lCudaKeySearchDevice -lcudadevrt -lcudart -Wl,--end-group -target x86_64-apple-macos10.12
endif
	mkdir -p $(BINDIR)
endif
ifeq ($(BUILD_OPENCL),1)
ifeq ($(PLATFORM),Darwin)
	${CXX} -DBUILD_OPENCL -o clKeyFinder-x86_64.bin ${CPPSRC} ${INCLUDE} -I${OPENCL_INCLUDE} ${CXXFLAGS} ${LIBS} $(OPENCL_LIB) -llogger -lcommonutils -lcmdparse -lsecp256k1 -lcryptoutil -lkeyfinder -laddressutil -lclutil -lCLKeySearchDevice -target x86_64-apple-macos10.12
else
	${CXX} -DBUILD_OPENCL -o clKeyFinder-x86_64.bin ${CPPSRC} ${INCLUDE} -I${OPENCL_INCLUDE} ${CXXFLAGS} ${LIBS} -Wl,--start-group $(OPENCL_LIB) -llogger -lcommonutils -lcmdparse -lsecp256k1 -lcryptoutil -lkeyfinder -laddressutil -lclutil -lCLKeySearchDevice -Wl,--end-group -target x86_64-apple-macos10.12
endif
	mkdir -p $(BINDIR)
endif

arm_app: ${CPPSRC}
ifeq ($(BUILD_CUDA), 1)
ifeq ($(PLATFORM),Darwin)
	${CXX} -DBUILD_CUDA -o cuKeyFinder-arm64.bin ${CPPSRC} ${INCLUDE} -I${CUDA_INCLUDE} ${CXXFLAGS} ${LIBS} ${OPENCL_LIB} -llogger -lcommonutils -lcmdparse -lsecp256k1 -lcryptoutil -lkeyfinder -laddressutil -lclutil -lclutil -lcudautil -lCudaKeySearchDevice -lcudadevrt -lcudart -target arm64-apple-macos11
else
	${CXX} -DBUILD_CUDA -o cuKeyFinder-arm64.bin ${CPPSRC} ${INCLUDE} -I${CUDA_INCLUDE} ${CXXFLAGS} ${LIBS} -Wl,--start-group ${OPENCL_LIB} -llogger -lcommonutils -lcmdparse -lsecp256k1 -lcryptoutil -lkeyfinder -laddressutil -lclutil -lclutil -lcudautil -lCudaKeySearchDevice -lcudadevrt -lcudart -Wl,--end-group -target arm64-apple-macos11
endif
	mkdir -p $(BINDIR)
endif
ifeq ($(BUILD_OPENCL),1)
ifeq ($(PLATFORM),Darwin)
	${CXX} -DBUILD_OPENCL -o clKeyFinder-arm64.bin ${CPPSRC} ${INCLUDE} -I${OPENCL_INCLUDE} ${CXXFLAGS} ${LIBS} $(OPENCL_LIB) -llogger -lcommonutils -lcmdparse -lsecp256k1 -lcryptoutil -lkeyfinder -laddressutil -lclutil -lCLKeySearchDevice -target arm64-apple-macos11
else
	${CXX} -DBUILD_OPENCL -o clKeyFinder-arm64.bin ${CPPSRC} ${INCLUDE} -I${OPENCL_INCLUDE} ${CXXFLAGS} ${LIBS} -Wl,--start-group $(OPENCL_LIB) -llogger -lcommonutils -lcmdparse -lsecp256k1 -lcryptoutil -lkeyfinder -laddressutil -lclutil -lCLKeySearchDevice -Wl,--end-group -target arm64-apple-macos11
endif
	mkdir -p $(BINDIR)
endif 

ifeq ($(BUILD_CUDA), 1)
universal_app: cuKeyFinder-x86_64.bin cuKeyFinder-arm64.bin
	lipo -create -output cuKeyFinder.bin cuKeyFinder-x86_64.bin cuKeyFinder-arm64.bin
	cp cuKeyFinder.bin $(BINDIR)/cuBitCrack
endif
ifeq ($(BUILD_OPENCL),1)
universal_app: clKeyFinder-x86_64.bin clKeyFinder-arm64.bin
	lipo -create -output clKeyFinder.bin clKeyFinder-x86_64.bin clKeyFinder-arm64.bin
	cp clKeyFinder.bin $(BINDIR)/clBitCrack
endif
endif

ifeq ($(BUILD_CUDA), 1)
ifeq ($(PLATFORM),Darwin)
sign0:
	export CODESIGN_ALLOCATE=/Applications/Xcode.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/../bin/codesign_allocate
	xcrun codesign -f -s $(SIGNING_IDENTITY) --preserve-metadata=identifier,entitlements --timestamp=none cuKeyFinder-arm64.bin
	
sign1:
	export CODESIGN_ALLOCATE=/Applications/Xcode.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/../bin/codesign_allocate
	xcrun codesign -f -s $(SIGNING_IDENTITY) --preserve-metadata=identifier,entitlements --timestamp=none cuKeyFinder-arm64.bin
	
sign2:
	export CODESIGN_ALLOCATE=/Applications/Xcode.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/../bin/codesign_allocate
	xcrun codesign -f -s $(SIGNING_IDENTITY) --preserve-metadata=identifier,entitlements --timestamp=none cuKeyFinder.bin
	
endif
endif
ifeq ($(BUILD_OPENCL),1)
ifeq ($(PLATFORM),Darwin)
sign0:
	export CODESIGN_ALLOCATE=/Applications/Xcode.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/../bin/codesign_allocate
	xcrun codesign -f -s $(SIGNING_IDENTITY) --preserve-metadata=identifier,entitlements --timestamp=none clKeyFinder-arm64.bin
	
sign1:
	export CODESIGN_ALLOCATE=/Applications/Xcode.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/../bin/codesign_allocate
	xcrun codesign -f -s $(SIGNING_IDENTITY) --preserve-metadata=identifier,entitlements --timestamp=none clKeyFinder-arm64.bin
	
sign2:
	export CODESIGN_ALLOCATE=/Applications/Xcode.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/../bin/codesign_allocate
	xcrun codesign -f -s $(SIGNING_IDENTITY) --preserve-metadata=identifier,entitlements --timestamp=none clKeyFinder.bin
	
endif

endif

clean:
	rm -rf *.gcno
	rm -rf *.gcda
	rm -rf cuKeyFinder.bin
	rm -rf clKeyFinder.bin
ifeq ($(PLATFORM),Darwin)
ifeq ($(BUILD_DEBUG),1)
	rm -rf cuKeyFinder.bin.dSYM
	rm -rf clKeyFinder.bin.dSYM
endif
endif
