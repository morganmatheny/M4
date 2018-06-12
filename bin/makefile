#!/bin/bash
# PETSC_DIR = /opt/petsc
# PETSC_ARCH = arch-darwin-c-debug

include ${PETSC_DIR}/lib/petsc/conf/variables
include ${PETSC_DIR}/lib/petsc/conf/rules

SRCDIR := src
LIBDIR := lib
BUILDDIR := build
TARGETDIR := bin
TARGET1 := $(TARGETDIR)/M4
TARGET2 := $(TARGETDIR)/conv

.DEFAULT_GOAL := all
.PHONY := clean

SRCEXT := c

SOURCES1 := $(filter-out $(SRCDIR)/convert.$(SRCEXT), $(wildcard $(SRCDIR)/*.$(SRCEXT)))
OBJECTS1 := $(patsubst $(SRCDIR)/%,$(BUILDDIR)/%,$(SOURCES1:.$(SRCEXT)=.o))

SOURCES2 := $(filter-out $(SRCDIR)/main.$(SRCEXT), $(wildcard $(SRCDIR)/*.$(SRCEXT)))
OBJECTS2 := $(patsubst $(SRCDIR)/%,$(BUILDDIR)/%,$(SOURCES2:.$(SRCEXT)=.o))

CFLAGS := -g -Wall -m64 -Os -Wwrite-strings -Wmissing-declarations -Wuninitialized
COMPILER = gcc

LIB := \
	-L${PETSC_DIR}/${PETSC_ARCH}/lib \
# 	-lmpi_cxx -lmpi -lm -ldl -lc #-static-libgcc

INCLUDE := \
	-Isrc/ \
	-I${PETSC_DIR}/include \
	-I${PETSC_DIR}/${PETSC_ARCH}/include 

all: ${TARGET1} ${TARGET2}
	xcodebuild -project ../M4.xcodeproj -alltargets -configuration Release

$(TARGET1): $(OBJECTS1) chkopts
	@mkdir -p $(TARGETDIR)
	@echo " Linking..."
	$(COMPILER) $(OBJECTS1) -o $(TARGET1) $(PETSC_LIB) $(LIB)

$(TARGET2): $(OBJECTS2) chkopts
	@mkdir -p $(TARGETDIR)
	@echo " Linking..."
	$(COMPILER) $(OBJECTS2) -o $(TARGET2) $(PETSC_LIB) $(LIB)

$(BUILDDIR)/%.o: $(SRCDIR)/%.$(SRCEXT)
	@echo ${PETSC_DIR}
	@mkdir -p $(BUILDDIR)
	$(COMPILER) $(CFLAGS) $(INCLUDE) -c -o $@ $<

clean_data:
	@echo " Cleaning...";
	$(RM) -r ../$(BUILDDIR)/* M4 ../output/* ../viz_dir/*

convert: 
	./conv -options_file ../input/main.in 

run:
	${PETSC_DIR}/${PETSC_ARCH}/bin/mpirun -np 4 ./M4 -options_file ../input/main.in
