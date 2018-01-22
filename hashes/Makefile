DOWNLOAD_DIR=downloads
HASH_DIR=hashes

GCC_URL = ftp://ftp.gnu.org/gnu/gcc
MPICH_URL = http://www.mpich.org/static/downloads

GCC_VERSIONS = 7.2.0 6.3.0 5.4.0 4.8.5
MPICH_VERSIONS = 3.2.1

GCC_HASHES=$(GCC_VERSIONS:%=$(HASH_DIR)/gcc-%.hash)
MPICH_HASHES=$(MPICH_VERSIONS:%=$(HASH_DIR)/mpich-%.hash)

.PHONY: all
.DEFAULT: all
all: $(GCC_HASHES) $(MPICH_HASHES)

$(HASH_DIR)/gcc-%.hash: | $(HASH_DIR)
	curl -sSL $(GCC_URL)/gcc-$*/gcc-$*.tar.gz | sha1sum | awk '{print $$1}'> $@

$(HASH_DIR)/mpich-%.hash: | $(HASH_DIR)
	curl -sSL $(MPICH_URL)/$*/mpich-$*.tar.gz | sha1sum | awk '{print $$1}'> $@

$(HASH_DIR): 
	mkdir -p $@

.PHONY: clean
clean:
	rm -rf $(HASH_DIR)
