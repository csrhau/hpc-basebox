DOWNLOAD_DIR=downloads
HASH_DIR=hashes

GCC_URL = ftp://ftp.gnu.org/gnu/gcc

GCC_VERSIONS = 7.2.0 6.3.0 5.4.0 4.8.5

GCC_HASHES=$(GCC_VERSIONS:%=$(HASH_DIR)/gcc-%.hash)

.PHONY: all
.DEFAULT: all
all: $(GCC_HASHES)

$(HASH_DIR)/gcc-%.hash: | $(HASH_DIR)
	curl -sSL $(GCC_URL)/gcc-$*/gcc-$*.tar.gz | sha1sum | awk '{print $$1}'> $@

$(HASH_DIR): 
	mkdir -p $@

.PHONY: clean
clean:
	rm -rf $(HASH_DIR)
