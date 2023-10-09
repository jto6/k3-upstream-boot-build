# Packaging variables
PKGNAME = k3-firmware
VERSION = 0
SUBLEVEL = 1
GIT_VERSION:= $(shell git describe --always --long --dirty || echo "unknown")
RELEASE_TAG:=$(VERSION).$(SUBLEVEL).$(GIT_VERSION)
RPMDIR= $(shell pwd)

.PHONY:	source-tarball
source-tarball:
	mkdir -p SOURCES/
	cd .. && \
		git archive --prefix=k3-upstream-boot-build/ -o rpm/SOURCES/k3-upstream-boot-build-$(RELEASE_TAG).tar.gz HEAD
	cd ../arm-trusted-firmware && \
		git archive --prefix=arm-trusted-firmware/ -o ../rpm/SOURCES/arm-trusted-firmware-$(RELEASE_TAG).tar.gz HEAD
	cd ../optee_os && \
		git archive --prefix=optee_os/ -o ../rpm/SOURCES/optee_os-$(RELEASE_TAG).tar.gz HEAD
	cd ../u-boot && \
		git archive --prefix=u-boot/ -o ../rpm/SOURCES/u-boot-$(RELEASE_TAG).tar.gz HEAD
	cd .. && \
		tar -czf rpm/SOURCES/ti-linux-firmware-$(RELEASE_TAG).tar.gz ti-linux-firmware/ti-dm \
		ti-linux-firmware/ti-sysfw

.PHONY:	gen-spec
gen-spec:
	mkdir -p SRPMS/
	sed "\
		s/%%RELEASE_TAG%%/$(RELEASE_TAG)/; \
		s/%%SPECTARFILE_RELEASE%%/$SPECTARFILE_RELEASE/" $(PKGNAME).spec.template > SRPMS/$(PKGNAME).spec

.PHONY: dist-sources
dist-sources: source-tarball gen-spec

.PHONY:	do-rpmbuild
do-rpmbuild: source-tarball
	rpmbuild $(RPMBUILDOPTS) --define "_sourcedir $(RPMDIR)/SOURCES" --define "_srcrpmdir $(RPMDIR)/SRPMS" \
		SRPMS/$(PKGNAME).spec

.PHONY: dist-srpm
dist-srpm: RPMBUILDOPTS=-bs
dist-srpm: dist-sources do-rpmbuild

.PHONY: dist-rpm
dist-rpm: RPMBUILDOPTS=-bb --define "soc j721e" --define "sectype gp"
dist-rpm: dist-sources do-rpmbuild

.PHONY: dist-clean
dist-clean:
	rm -rf SOURCES SRPMS
