NAME=CLKeySearchDevice
LIBSRC=$(wildcard *.cpp)
LIBSRC:=$(LIBSRC) bitcrack_cl.cpp
LIBOBJ=$(LIBSRC:.cpp=.o)
TARGETLIB=lib$(NAME).a

LOADLIBS = $(LIBS)
CXXFLAGS := $(INCLUDE) -I$(OPENCL_INCLUDE) $(CXXFLAGS) $(LOADLIBS) -Wl,--start-group $(OPENCL_LIB) -llogger -lcommonutils -lsecp256k1 -lcryptoutil -laddressutil -lclutil -lkeyfinder -Wl,--end-group

$(TARGETLIB): $(LIBSRC)

TARGETS = $(TARGETLIB)

ifeq ($(PLATFORM),Darwin)
SIGNING_IDENTITY=$(CODER)
endif

ifeq ($(PLATFORM),Darwin)
.PHONY: all sign0 sign1 sign2 install uninstall clean

all: bitcrack_cl.cpp x86_app sign0 arm_app sign1 universal_app sign2 

bitcrack_cl.cpp:
	rm -rf bitcrack.cl
	rm -rf bitcrack_cl.cpp
	cat ../clMath/sha256.cl ../clMath/secp256k1.cl ../clMath/ripemd160.cl bloomfilter.cl bitcoin.cl keysearch.cl > bitcrack.cl
	$(BINDIR)/embedcl bitcrack.cl bitcrack_cl.cpp _bitcrack_cl

x86_app: $(LIBSRC)
	for file in $(LIBSRC); do\
		$(CXX) $(CXXFLAGS) -c $$file;\
	done

$(TARGETS): $(LIBOBJ)
	mkdir -p $(LIBDIR)
	$(CXX)$(LDFLAGS) $(LOADLIBS) $(OPENCL_LIB) -llogger -lcommonutils -lsecp256k1 -lcryptoutil -laddressutil -lclutil -lkeyfinder -dynamiclib -undefined suppress -flat_namespace $(LIBOBJ) -o $(LIBDIR)/lib$(NAME)-x86_64.dylib -target x86_64-apple-macos10.12

arm_app: $(LIBSRC)
	for file in $(LIBSRC); do\
		$(CXX) $(CXXFLAGS) -c $$file;\
	done

$(TARGETS): $(LIBOBJ)
	mkdir -p $(LIBDIR)
	$(CXX)$(LDFLAGS) $(LOADLIBS) $(OPENCL_LIB) -llogger -lcommonutils -lsecp256k1 -lcryptoutil -laddressutil -lclutil -lkeyfinder -dynamiclib -undefined suppress -flat_namespace $(LIBOBJ) -o $(LIBDIR)/lib$(NAME)-arm64.dylib -target arm64-apple-macos11

universal_app: $(LIBDIR)/lib$(NAME)-x86_64.dylib $(LIBDIR)/lib$(NAME)-arm64.dylib
	lipo -create -output $(LIBDIR)/lib$(NAME).dylib $(LIBDIR)/lib$(NAME)-x86_64.dylib $(LIBDIR)/lib$(NAME)-arm64.dylib
endif

ifeq ($(PLATFORM),Darwin)
sign0:
	export CODESIGN_ALLOCATE=/Applications/Xcode.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/../bin/codesign_allocate
	xcrun codesign -f -s $(SIGNING_IDENTITY) --preserve-metadata=identifier,entitlements --timestamp=none $(LIBDIR)/lib$(NAME)-x86_64.dylib
	
sign1:
	export CODESIGN_ALLOCATE=/Applications/Xcode.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/../bin/codesign_allocate
	xcrun codesign -f -s $(SIGNING_IDENTITY) --preserve-metadata=identifier,entitlements --timestamp=none $(LIBDIR)/lib$(NAME)-arm64.dylib
	
sign2:
	export CODESIGN_ALLOCATE=/Applications/Xcode.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/../bin/codesign_allocate
	xcrun codesign -f -s $(SIGNING_IDENTITY) --preserve-metadata=identifier,entitlements --timestamp=none $(LIBDIR)/lib$(NAME).dylib
	
endif

clean:
	rm -rf *.a
	rm -rf *.o
	rm -rf *.gcno
	rm -rf *.gcda
	rm -rf bitcrack.cl
	rm -rf bitcrack_cl.cpp
