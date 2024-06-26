# Compiler tools, commands, and flags
COMPILE_X86         := i686-pc-win32-msvc-gcc
COMPILE_X64         := x86_64-pc-win32-msvc-gcc
COMPILE_ARM         := arm-pc-win32-msvc-gcc
COMPILE_ARM64       := aarch64-pc-win32-msvc-gcc
LINK_X86            := i686-pc-win32-msvc-ld
LINK_X64            := x86_64-pc-win32-msvc-ld
LINK_ARM            := arm-pc-win32-msvc-ld
LINK_ARM64          := aarch64-pc-win32-msvc-ld
CFLAGS              := -Os -flto
CFLAGS_X86          := -O0
LDFLAGS             := -flto -Wl,/DLL
LDFLAGS_X86         := -Wl,/DLL
JDK_HOME            := $(shell echo "`dirname $$(dirname $$(readlink -f $$(which javac)))`")
INCLUDES            := -I"$(JDK_HOME)/include" -Iwin32
LIBRARIES           := -lAdvAPI32 -lSetupAPI -lshell32
DELETE              := @rm
MKDIR               := @mkdir
COPY                := @cp
MOVE                := @mv
PRINT               := @echo

# Java and resource definitions
LIBRARY_NAME        := jSerialComm.dll
BUILD_DIR           := ../../../../build
RESOURCE_DIR        := ../../../../src/main/resources/Windows
JAVA_SOURCE_DIR     := ../../../../src/main/java/com/fazecast/jSerialComm
FULL_CLASS          := com.fazecast.jSerialComm.SerialPort
JAVA_CLASS_DIR      := $(BUILD_DIR)/com/fazecast/jSerialComm
JNI_HEADER_FILE     := com_fazecast_jSerialComm_SerialPort.h
JNI_HEADER          := ../$(JNI_HEADER_FILE)
ANDROID_JAR         := ../../../external/android.jar
JAVA_CLASS          := $(JAVA_CLASS_DIR)/SerialPort.class
JFLAGS              := --release 6 -Xlint:-options
JAVAC               := "$(JDK_HOME)/bin/javac"

# Architecture-dependent library variables
OBJECTSx86          := $(BUILD_DIR)/x86/SerialPort_Windows.o $(BUILD_DIR)/x86/WindowsHelperFunctions.o
OBJECTSx64          := $(BUILD_DIR)/x86_64/SerialPort_Windows.o $(BUILD_DIR)/x86_64/WindowsHelperFunctions.o
OBJECTSarm          := $(BUILD_DIR)/armv7/SerialPort_Windows.o $(BUILD_DIR)/armv7/WindowsHelperFunctions.o
OBJECTSarm64        := $(BUILD_DIR)/aarch64/SerialPort_Windows.o $(BUILD_DIR)/aarch64/WindowsHelperFunctions.o

# Define phony and suffix rules
.PHONY: all win32 win64 winarm winarm64 checkdirs clean
.SUFFIXES:
.SUFFIXES: .c .o .class .java .h

# Default build target not possible due to different architecture compilers
all : win32 win64 winarm winarm64
	$(DELETE) -rf "$(BUILD_DIR)"
	$(DELETE) -rf ../*.h $(JNI_HEADER_FILE)
clean :
	$(DELETE) -rf "$(BUILD_DIR)"
	$(DELETE) -rf ../*.h $(JNI_HEADER_FILE)

# Build architecture-specific Windows libraries
win32 : $(JAVA_CLASS_DIR) $(BUILD_DIR)/x86 $(BUILD_DIR)/x86/$(LIBRARY_NAME)
	$(MKDIR) -p $(RESOURCE_DIR)/x86/
	$(COPY) $(BUILD_DIR)/x86/*.dll $(RESOURCE_DIR)/x86/
win64 : $(JAVA_CLASS_DIR) $(BUILD_DIR)/x86_64 $(BUILD_DIR)/x86_64/$(LIBRARY_NAME)
	$(MKDIR) -p $(RESOURCE_DIR)/x86_64
	$(COPY) $(BUILD_DIR)/x86_64/*.dll $(RESOURCE_DIR)/x86_64
winarm : $(JAVA_CLASS_DIR) $(BUILD_DIR)/armv7 $(BUILD_DIR)/armv7/$(LIBRARY_NAME)
	$(MKDIR) -p $(RESOURCE_DIR)/armv7/
	$(COPY) $(BUILD_DIR)/armv7/*.dll $(RESOURCE_DIR)/armv7/
winarm64 : $(JAVA_CLASS_DIR) $(BUILD_DIR)/aarch64 $(BUILD_DIR)/aarch64/$(LIBRARY_NAME)
	$(MKDIR) -p $(RESOURCE_DIR)/aarch64/
	$(COPY) $(BUILD_DIR)/aarch64/*.dll $(RESOURCE_DIR)/aarch64/

# Rule to create build directories
$(BUILD_DIR)/x86 :
	$(MKDIR) -p $@
$(BUILD_DIR)/x86_64 :
	$(MKDIR) -p $@
$(BUILD_DIR)/armv7 :
	$(MKDIR) -p $@
$(BUILD_DIR)/aarch64 :
	$(MKDIR) -p $@
$(JAVA_CLASS_DIR) :
	$(MKDIR) -p $@

# Build rules for all libraries
$(BUILD_DIR)/x86/$(LIBRARY_NAME) : $(JNI_HEADER) $(OBJECTSx86)
	$(LINK_X86) $(LDFLAGS_X86) -o $@ $(OBJECTSx86) $(LIBRARIES)
$(BUILD_DIR)/x86_64/$(LIBRARY_NAME) : $(JNI_HEADER) $(OBJECTSx64)
	$(LINK_X64) $(LDFLAGS) -o $@ $(OBJECTSx64) $(LIBRARIES)
$(BUILD_DIR)/armv7/$(LIBRARY_NAME) : $(JNI_HEADER) $(OBJECTSarm)
	$(LINK_ARM) $(LDFLAGS) -o $@ $(OBJECTSarm) $(LIBRARIES)
$(BUILD_DIR)/aarch64/$(LIBRARY_NAME) : $(JNI_HEADER) $(OBJECTSarm64)
	$(LINK_ARM64) $(LDFLAGS) -o $@ $(OBJECTSarm64) $(LIBRARIES)

# Suffix rules to get from *.c -> *.o
$(BUILD_DIR)/x86/%.o : %.c
	$(COMPILE_X86) $(INCLUDES) $(CFLAGS_X86) -c $< -o $@
$(BUILD_DIR)/x86_64/%.o : %.c
	$(COMPILE_X64) $(INCLUDES) $(CFLAGS) -c $< -o $@
$(BUILD_DIR)/armv7/%.o : %.c
	$(COMPILE_ARM) $(INCLUDES) $(CFLAGS) -c $< -o $@
$(BUILD_DIR)/aarch64/%.o : %.c
	$(COMPILE_ARM64) $(INCLUDES) $(CFLAGS) -c $< -o $@

# Rule to build JNI header file
$(JNI_HEADER) : $(JAVA_CLASS)
	$(COPY) $@ ./

# Suffix rule to get from *.java -> *.class
$(JAVA_CLASS) :
	$(JAVAC) $(JFLAGS) -d $(JAVA_CLASS_DIR)/../../.. -cp $(JAVA_SOURCE_DIR)/../../..:$(ANDROID_JAR) $(JAVA_SOURCE_DIR)/$(basename $(notdir $@)).java -h ..
