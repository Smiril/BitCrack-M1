NAME=clutil
SRC=$(wildcard *.cpp)
OBJS=$(SRC:.cpp=.o)

ifeq ($(PLATFORM),Darwin)
SIGNING_IDENTITY=$(CODER)
endif

ifeq ($(PLATFORM),Darwin)
.PHONY: all sign0 sign1 sign2 install uninstall clean

all: x86_app sign0 arm_app sign1 universal_app sign2

x86_app: ${SRC}
	for file in ${SRC} ; do\
		${CXX} ${INCLUDE} -I${OPENCL_INCLUDE} ${CXXFLAGS} ${LIBS} -c $$file ${OPENCL_LIB} -lcommonutils;\
	done
	mkdir -p ${LIBDIR}
	${CXX} ${LDFLAGS} -dynamiclib -undefined suppress -flat_namespace ${OBJS} -o ${LIBDIR}/lib$(NAME)-x86_64.dylib -target x86_64-apple-macos10.12

arm_app: ${SRC}
	for file in ${SRC} ; do\
		${CXX} ${INCLUDE} -I${OPENCL_INCLUDE} ${CXXFLAGS} ${LIBS} -c $$file ${OPENCL_LIB} -lcommonutils;\
	done
	mkdir -p ${LIBDIR}
	${CXX} ${LDFLAGS} -dynamiclib -undefined suppress -flat_namespace ${OBJS} -o ${LIBDIR}/lib$(NAME)-arm64.dylib -target arm64-apple-macos11

universal_app: ${LIBDIR}/lib$(NAME)-x86_64.dylib ${LIBDIR}/lib$(NAME)-arm64.dylib
	lipo -create -output ${LIBDIR}/lib$(NAME).dylib ${LIBDIR}/lib$(NAME)-x86_64.dylib ${LIBDIR}/lib$(NAME)-arm64.dylib
endif

ifeq ($(PLATFORM),Darwin)
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
	rm -rf *.o
	rm -rf *.a
	rm -rf *.gcno
	rm -rf *.gcda
