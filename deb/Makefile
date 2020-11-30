### Makefile ---

## Author: shell@xps13
## Version: $Id: Makefile,v 0.0 2017/03/22 04:56:03 shell Exp $
## Keywords:
## X-URL:

# you need to install the libtemplate-perl package first to get the tpage utility.
# we use Perl TT2 templates to generate */debian/changelog files.

JOBS=8
ZLIB_VER=1.2.11
PCRE_VER=8.44
SSL_VER=1.1.0l
SSL111_VER=1.1.1h
OR_VER=1.19.3.1
LEMPLATE_VER=0.15
TEST_NGINX_VER=0.29
OPTS=$(DEBUILD_OPTS)
DISTRO:=$(shell lsb_release -cs)
OPTS=

.PHONY: build
build: build-prereqs \
	zlib-build \
	pcre-build \
	openssl111-build \
	openresty-build \
	lemplate-build \
	test-nginx-build

.PHONY: clean
clean: build-prereqs \
	zlib-clean \
	pcre-clean \
	openssl111-clean \
	openresty-clean \
	lemplate-clean \
	test-nginx-clean

.PHONY: distclean
distclean: clean
	-rm -f *.gz *.bz2 *.xz *.zip

.PHONY: download
download: zlib-download \
	pcre-download \
	openssl111-download \
	openresty-download \
	lemplate-download \
	test-nginx-download

.PHONY: build-prereqs
build-prereqs:
ifneq ($(shell id -u), 0)
	sudo apt-get -qq -y install autotools-dev debhelper wget libtemplate-perl
else
	apt-get -qq -y install autotools-dev debhelper wget libtemplate-perl
endif

.PHONY: zlib-download
zlib-download:
	rm -f openresty-zlib_$(ZLIB_VER).orig.tar.gz
	wget -nH --cut-dirs=100 --mirror 'http://www.zlib.net/fossils/zlib-$(ZLIB_VER).tar.gz'
	cp zlib-$(ZLIB_VER).tar.gz openresty-zlib_$(ZLIB_VER).orig.tar.gz

.PHONY: zlib-build
zlib-build: | build-prereqs zlib-clean zlib-download
	tar xf openresty-zlib_$(ZLIB_VER).orig.tar.gz --strip-components=1 -C openresty-zlib
	cd openresty-zlib \
		&& tpage --define distro=$(DISTRO) debian/changelog.tt2 > debian/changelog \
		&& debuild $(OPTS) -j$(JOBS)
	#for f in *.deb; do debsigs --sign=origin -K D5EDEB74 $$f || exit 1; done
	if [ -f ./upload ]; then ./upload || exit 1; fi

.PHONY: zlib-clean
zlib-clean: build-prereqs
	cd openresty-zlib && debclean
	find openresty-zlib -maxdepth 1 ! -name 'debian' ! -name 'openresty-zlib' -print | xargs rm -rf
	rm -f openresty-zlib_$(ZLIB_VER)*.deb
	rm -f openresty-zlib-dev_$(ZLIB_VER)*.deb
	rm -f openresty-zlib-dbgsym_$(ZLIB_VER)*.deb
	rm -f openresty-zlib_$(ZLIB_VER)*.*

.PHONY: pcre-download
pcre-download:
	rm -f openresty-pcre_$(PCRE_VER).orig.tar.bz2
	wget -nH --cut-dirs=100 --mirror 'https://ftp.pcre.org/pub/pcre/pcre-$(PCRE_VER).tar.bz2'
	cp pcre-$(PCRE_VER).tar.bz2 openresty-pcre_$(PCRE_VER).orig.tar.bz2

.PHONY: pcre-build
pcre-build: | build-prereqs pcre-clean pcre-download
	tar xf openresty-pcre_$(PCRE_VER).orig.tar.bz2 --strip-components=1 -C openresty-pcre
	cd openresty-pcre \
		&& tpage --define distro=$(DISTRO) debian/changelog.tt2 > debian/changelog \
		&& debuild $(OPTS) -j$(JOBS)
	#for f in openresty-pcre*.deb; do debsigs --sign=origin -K D5EDEB74 $$f || exit 1; done
	if [ -f ./upload ]; then ./upload || exit 1; fi

.PHONY: pcre-clean
pcre-clean:
	cd openresty-pcre && debclean
	find openresty-pcre -maxdepth 1 ! -name 'debian' ! -name 'openresty-pcre' -print | xargs rm -rf
	rm -f openresty-pcre_$(PCRE_VER)*.deb
	rm -f openresty-pcre-dev_$(PCRE_VER)*.deb
	rm -f openresty-pcre-dbgsym_$(PCRE_VER)*.deb
	rm -f openresty-pcre_$(PCRE_VER)*.*

.PHONY: openssl111-download
openssl111-download:
	rm -f openresty-openssl111_$(SSL111_VER).orig.tar.gz
	wget -nH --cut-dirs=100 --mirror 'https://www.openssl.org/source/openssl-$(SSL111_VER).tar.gz'
	cp openssl-$(SSL111_VER).tar.gz openresty-openssl111_$(SSL111_VER).orig.tar.gz

.PHONY: openssl111-build
openssl111-build: | build-prereqs openssl111-prereqs openssl111-clean openssl111-download
	tar xf openresty-openssl111_$(SSL111_VER).orig.tar.gz --strip-components=1 -C openresty-openssl111
	cd openresty-openssl111 \
		&& tpage --define distro=$(DISTRO) debian/changelog.tt2 > debian/changelog \
		&& debuild $(OPTS) -j$(JOBS)
	#for f in openresty-openssl111*.deb; do debsigs --sign=origin -K D5EDEB74 $$f || exit 1; done
	if [ -f ./upload ]; then ./upload || exit 1; fi

.PHONY: openssl111-prereqs
openssl111-prereqs:
ifneq ($(shell id -u), 0)
ifeq ("$(wildcard $(openresty-zlib-dev_$(ZLIB_VER)*.deb))","")
	sudo dpkg -i openresty-zlib-dev_$(ZLIB_VER)*.deb openresty-zlib_$(ZLIB_VER)*.deb
else
	sudo apt-get -qq -y install openresty-zlib-dev
endif
else
ifeq ("$(wildcard $(openresty-zlib-dev_$(ZLIB_VER)*.deb))","")
	dpkg -i openresty-zlib-dev_$(ZLIB_VER)*.deb openresty-zlib_$(ZLIB_VER)*.deb
else
	apt-get -qq -y install openresty-zlib-dev
endif
endif

.PHONY: openssl111-clean
openssl111-clean:
	-cd openresty-openssl111 && debclean
	rm -rf openresty-openssl111/debian/tmp openresty-openssl111/test openresty-openssl111/*.a
	find openresty-openssl111 -maxdepth 1 ! -name 'debian' ! -name 'openresty-openssl111' -print | xargs rm -rf
	rm -f openresty-openssl111_$(SSL111_VER)*.deb
	rm -f openresty-openssl111-dev_$(SSL111_VER)*.deb
	rm -f openresty-openssl111-dbgsym_$(SSL111_VER)*.deb
	rm -f openresty-openssl111_$(SSL111_VER)*.*

.PHONY: openresty-download
openresty-download:
	rm -f openresty_$(OR_VER).orig.tar.gz
	wget -nH --cut-dirs=100 --mirror 'https://openresty.org/download/openresty-$(OR_VER).tar.gz'
	cp openresty-$(OR_VER).tar.gz openresty_$(OR_VER).orig.tar.gz

.PHONY: openresty-build
openresty-build: | build-prereqs openresty-prereqs openresty-clean openresty-download
	tar xf openresty_$(OR_VER).orig.tar.gz --strip-components=1 -C openresty
	cd openresty \
		&& tpage --define distro=$(DISTRO) debian/changelog.tt2 > debian/changelog \
		&& debuild $(OPTS) -j$(JOBS)
	#for f in openresty*.deb; do debsigs --sign=origin -K D5EDEB74 $$f || exit 1; done
	if [ -f ./upload ]; then ./upload || exit 1; fi

.PHONY: openresty-prereqs
openresty-prereqs:
ifneq ($(shell id -u), 0)
ifeq ("$(wildcard $(openresty-zlib-dev_$(ZLIB_VER)*.deb))","")
	sudo dpkg -i openresty-zlib-dev_$(ZLIB_VER)*.deb openresty-zlib_$(ZLIB_VER)*.deb
else
	sudo apt-get -qq -y install openresty-zlib-dev
endif
ifeq ("$(wildcard $(openresty-pcre-dev_$(PCRE_VER)*.deb))","")
	sudo dpkg -i openresty-pcre-dev_$(PCRE_VER)*.deb openresty-pcre_$(PCRE_VER)*.deb
else
	sudo apt-get -qq -y install openresty-pcre-dev
endif
ifeq ("$(wildcard $(openresty-openssl111-dev_$(SSL111_VER)*.deb))","")
	sudo dpkg -i openresty-openssl111-dev_$(SSL111_VER)*.deb openresty-openssl111_$(SSL111_VER)*.deb
else
	sudo apt-get -qq -y install openresty-openssl111-dev
endif
	sudo apt-get -qq -y install systemtap-sdt-dev dh-systemd libmodsecurity-dev
else
ifeq ("$(wildcard $(openresty-zlib-dev_$(ZLIB_VER)*.deb))","")
	dpkg -i openresty-zlib-dev_$(ZLIB_VER)*.deb openresty-zlib_$(ZLIB_VER)*.deb
else
	apt-get -qq -y install openresty-zlib-dev
endif
ifeq ("$(wildcard $(openresty-pcre-dev_$(PCRE_VER)*.deb))","")
	dpkg -i openresty-pcre-dev_$(PCRE_VER)*.deb openresty-pcre_$(PCRE_VER)*.deb
else
	apt-get -qq -y install openresty-pcre-dev
endif
ifeq ("$(wildcard $(openresty-openssl111-dev_$(SSL111_VER)*.deb))","")
	dpkg -i openresty-openssl111-dev_$(SSL111_VER)*.deb openresty-openssl111_$(SSL111_VER)*.deb
else
	apt-get -qq -y install openresty-openssl111-dev
endif
	apt-get -qq -y install systemtap-sdt-dev dh-systemd libmodsecurity-dev
endif

.PHONY: openresty-clean
openresty-clean:
	cd openresty && debclean
	find openresty -maxdepth 1 ! -name 'debian' ! -name 'openresty' -print | xargs rm -rf
	rm -f openresty_$(OR_VER)*.deb
	rm -f openresty-opm_$(OR_VER)*.deb
	rm -f openresty-resty_$(OR_VER)*.deb
	rm -f openresty-restydoc_$(OR_VER)*.deb
	rm -f openresty-dev_$(OR_VER)*.deb
	rm -f openresty-dbgsym_$(OR_VER)*.deb
	rm -f openresty_$(OR_VER)*.*

.PHONY: lemplate-download
lemplate-download:
	rm -f liblemplate-perl_$(LEMPLATE_VER).orig.tar.gz
	wget -nH --cut-dirs=100 --mirror 'http://search.cpan.org/CPAN/authors/id/A/AG/AGENT/Lemplate-$(LEMPLATE_VER).tar.gz'
	cp Lemplate-$(LEMPLATE_VER).tar.gz liblemplate-perl_$(LEMPLATE_VER).orig.tar.gz

.PHONY: lemplate-clean
lemplate-clean:
	-cd liblemplate-perl/ && debclean
	find liblemplate-perl -maxdepth 1 ! -name 'debian' ! -name 'liblemplate-perl' -print | xargs rm -rf
	rm -f liblemplate-perl_$(LEMPLATE_VER)*.deb
	rm -f liblemplate-perl_$(LEMPLATE_VER)*.*

.PHONY: lemplate-build
lemplate-build: | build-prereqs lemplate-prereqs lemplate-clean lemplate-download
	tar xf liblemplate-perl_$(LEMPLATE_VER).orig.tar.gz --strip-components=1 -C liblemplate-perl
	cd liblemplate-perl \
		&& tpage --define distro=$(DISTRO) debian/changelog.tt2 > debian/changelog \
		&& debuild -sa $(OPTS) -j$(JOBS)
	#for f in openresty-valgrind*.deb; do debsigs --sign=origin -K D5EDEB74 $$f || exit 1; done
	if [ -f ./upload ]; then ./upload || exit 1; fi

.PHONY: lemplate-prereqs
lemplate-prereqs:
ifneq ($(shell id -u), 0)
	sudo apt-get -qq -y install libipc-run3-perl libfile-find-rule-perl libtext-glob-perl libnumber-compare-perl
else
	apt-get -qq -y install libipc-run3-perl libfile-find-rule-perl libtext-glob-perl libnumber-compare-perl
endif

.PHONY: test-nginx-download
test-nginx-download:
	rm -f libtest-nginx-perl_$(TEST_NGINX_VER).orig.tar.gz
	wget -nH --cut-dirs=100 --mirror 'http://search.cpan.org/CPAN/authors/id/A/AG/AGENT/Test-Nginx-$(TEST_NGINX_VER).tar.gz'
	cp Test-Nginx-$(TEST_NGINX_VER).tar.gz libtest-nginx-perl_$(TEST_NGINX_VER).orig.tar.gz

.PHONY: test-nginx-clean
test-nginx-clean:
	-cd libtest-nginx-perl/ && debclean
	find libtest-nginx-perl -maxdepth 1 ! -name 'debian' ! -name 'libtest-nginx-perl' -print | xargs rm -rf
	rm -f libtest-nginx-perl_$(TEST_NGINX_VER)*.deb
	rm -f libtest-nginx-perl_$(TEST_NGINX_VER)*.*

.PHONY: test-nginx-build
test-nginx-build: | build-prereqs test-nginx-prereqs test-nginx-clean test-nginx-download
	tar xf libtest-nginx-perl_$(TEST_NGINX_VER).orig.tar.gz --strip-components=1 -C libtest-nginx-perl
	cd libtest-nginx-perl \
		&& tpage --define distro=$(DISTRO) debian/changelog.tt2 > debian/changelog \
		&& debuild -sa $(OPTS) -j$(JOBS)
	#for f in openresty-valgrind*.deb; do debsigs --sign=origin -K D5EDEB74 $$f || exit 1; done
	if [ -f ./upload ]; then ./upload || exit 1; fi

.PHONY: test-nginx-prereqs
test-nginx-prereqs:
ifneq ($(shell id -u), 0)
	sudo apt-get -qq -y install libtest-base-perl libtest-longstring-perl libtext-diff-perl liblist-moreutils-perl
else
	apt-get -qq -y install libtest-base-perl libtest-longstring-perl libtext-diff-perl liblist-moreutils-perl
endif

### Makefile ends here
