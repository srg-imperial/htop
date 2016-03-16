SH     = $(shell which sh)
PWD    = $(shell pwd)

# Varan
VX     = $(shell which vx)

# Compilers
GCC    = $(shell which gcc)
CLANG  = $(shell which clang)

# For debugging
STRIP  = $(shell which true)
CFLAGS = -O0 -g

# Where each version expects to be installed
INSTALL_DIR       = $(PWD)/install
INSTALL_BUILD_DIR = $(INSTALL_DIR)/htop

# Where different versions are installed
INSTALL_GCC        = $(INSTALL_DIR)/gcc-system
INSTALL_GCC_ASAN   = $(INSTALL_DIR)/gcc-asan-system
INSTALL_CLANG      = $(INSTALL_DIR)/clang-system
INSTALL_CLANG_ASAN = $(INSTALL_DIR)/clang-asan-system

# Binaries inside each build directory
HTOP_GCC        = $(INSTALL_GCC)/bin/htop
HTOP_GCC_ASAN   = $(INSTALL_GCC_ASAN)/bin/htop
HTOP_CLANG      = $(INSTALL_CLANG)/bin/htop
HTOP_CLANG_ASAN = $(INSTALL_CLANG_ASAN)/bin/htop

# Script to run HTOP with Varan
HTOP_DIR = $(PWD)/bin
HTOP     = $(HTOP_DIR)/htop

# Command lines to run HTOP with Varan inside script
RUN_GCC_GCC          =                              $(VX) $(HTOP_GCC) $(HTOP_GCC)          -- \$$@
RUN_GCC_GCC_ASAN     = ASAN_OPTIONS="handle_segv=0" $(VX) $(HTOP_GCC) $(HTOP_GCC_ASAN)     -- \$$@
RUN_CLANG_CLANG      =                              $(VX) $(HTOP_CLANG) $(HTOP_CLANG)      -- \$$@
RUN_CLANG_CLANG_ASAN = ASAN_OPTIONS="handle_segv=0" $(VX) $(HTOP_CLANG) $(HTOP_CLANG_ASAN) -- \$$@

# Main target
all: vx-gcc-gcc_asan

# ==============================================================================
# GCC targets
# ==============================================================================

# Set up script for native leader and native follower built with gcc
vx-gcc-gcc: $(HTOP_GCC)
	rm -rf $(INSTALL_BUILD_DIR) || true
	cp -r $(INSTALL_GCC) $(INSTALL_BUILD_DIR)
	rm -rf $(HTOP_DIR) || true
	mkdir -p $(HTOP_DIR)
	echo "#!$(SH)"        >  $(HTOP)
	echo "$(RUN_GCC_GCC)" >> $(HTOP)
	chmod a+x $(HTOP)

# Set up script for native leader and sanitized follower built with gcc
vx-gcc-gcc_asan: $(HTOP_GCC) $(HTOP_GCC_ASAN)
	rm -rf $(INSTALL_BUILD_DIR) || true
	cp -r $(INSTALL_GCC) $(INSTALL_BUILD_DIR)
	rm -rf $(HTOP_DIR) || true
	mkdir -p $(HTOP_DIR)
	echo "#!$(SH)"             >  $(HTOP)
	echo "$(RUN_GCC_GCC_ASAN)" >> $(HTOP)
	chmod a+x $(HTOP)

# Build using gcc and no sanitization
$(HTOP_GCC):
	rm -rf $(INSTALL_BUILD_DIR) || true
	make -C .. clean || true
	cd .. ; ./autogen.sh || true
	cd .. ; CC=$(GCC)  \
  	CFLAGS="$(CFLAGS)" \
  	STRIP="$(STRIP)"   \
  	./configure        \
  	--prefix=$(INSTALL_BUILD_DIR)
	make -C ..
	make -C .. install
	rm -rf $(INSTALL_GCC) || true
	mv $(INSTALL_BUILD_DIR) $(INSTALL_GCC)

# Build using gcc and sanitization
$(HTOP_GCC_ASAN):
	rm -rf $(INSTALL_BUILD_DIR) || true
	make -C .. clean || true
	cd .. ; ./autogen.sh || true
	cd .. ; CC=$(GCC)                     \
  	CFLAGS="$(CFLAGS) -fsanitize=address" \
  	LDFLAGS="-fsanitize=address"          \
  	STRIP="$(STRIP)"                      \
  	./configure                           \
  	--prefix=$(INSTALL_BUILD_DIR)
	make -C ..
	make -C .. install
	rm -rf $(INSTALL_GCC_ASAN) || true
	mv $(INSTALL_BUILD_DIR) $(INSTALL_GCC_ASAN)

# ==============================================================================
# CLANG targets
# ==============================================================================

# Set up script for native leader and native follower built with clang
vx-clang-clang: $(HTOP_CLANG)
	rm -rf $(INSTALL_BUILD_DIR) || true
	cp -r $(INSTALL_CLANG) $(INSTALL_BUILD_DIR)
	rm -rf $(HTOP_DIR) || true
	mkdir -p $(HTOP_DIR)
	echo "#!$(SH)"            >  $(HTOP)
	echo "$(RUN_CLANG_CLANG)" >> $(HTOP)
	chmod a+x $(HTOP)

# Set up script for native leader and sanitized follower built with clang
vx-clang-clang_asan: $(HTOP_CLANG) $(HTOP_CLANG_ASAN)
	rm -rf $(INSTALL_BUILD_DIR) || true
	cp -r $(INSTALL_CLANG) $(INSTALL_BUILD_DIR)
	rm -rf $(HTOP_DIR) || true
	mkdir -p $(HTOP_DIR)
	echo "#!$(SH)"                 >  $(HTOP)
	echo "$(RUN_CLANG_CLANG_ASAN)" >> $(HTOP)
	chmod a+x $(HTOP)

# Build using clang and no sanitization
$(HTOP_CLANG):
	rm -rf $(INSTALL_BUILD_DIR) || true
	make -C .. clean || true
	cd .. ; ./autogen.sh || true
	cd .. ; CC=$(CLANG) \
  	CFLAGS="$(CFLAGS)"  \
  	STRIP="$(STRIP)"    \
  	./configure         \
  	--prefix=$(INSTALL_BUILD_DIR)
	make -C ..
	make -C .. install
	rm -rf $(INSTALL_CLANG) || true
	mv $(INSTALL_BUILD_DIR) $(INSTALL_CLANG)

# Build using clang and sanitization
$(HTOP_CLANG_ASAN):
	rm -rf $(INSTALL_BUILD_DIR) || true
	make -C .. clean || true
	cd .. ; ./autogen.sh || true
	cd .. ; CC=$(CLANG)                   \
  	CFLAGS="$(CFLAGS) -fsanitize=address" \
  	LDFLAGS="-fsanitize=address"          \
  	STRIP="$(STRIP)"                      \
  	./configure                           \
  	--prefix=$(INSTALL_BUILD_DIR)
	make -C ..
	make -C .. install
	rm -rf $(INSTALL_CLANG_ASAN) || true
	mv $(INSTALL_BUILD_DIR) $(INSTALL_CLANG_ASAN)

# ==============================================================================
# misc targets
# ==============================================================================

clean:
	make -C .. clean || true
	rm -rf $(INSTALL_DIR) || true
	rm -rf $(HTOP_DIR) || true
