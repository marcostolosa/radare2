include ../global.mk
include ../libr/config.mk

DESTDIR:=$(DESTDIR)
BTOP=$(shell pwd)

.PHONY: all clean install install-symlink deinstall uninstall mrproper preload

BINS=rax2 rasm2 rabin2 rahash2 radiff2 radare2 rafind2 rarun2 ragg2 r2agent

LIBR2=$(call libname-version,libr2.$(EXT_SO),${LIBVERSION})

all: preload
	@for BINARY in $(BINS) ; do $(MAKE) -C $${BINARY} all; done
ifeq ($(COMPILER),ios-sdk)
	$(MAKE) ios-sign
endif

preload:
ifeq ($(WITHPIC),1)
	$(MAKE) -C preload
endif

install:
	mkdir -p "${DESTDIR}${BINDIR}"
	for BINARY in ${BINS} ; do \
		rm -f "${DESTDIR}${BINDIR}/$$BINARY" ; \
		${INSTALL_PROGRAM} "$$BINARY/$$BINARY" "${DESTDIR}${BINDIR}/$$BINARY" ; \
	done
	${INSTALL_SCRIPT} "$(BTOP)/r2pm/r2pm" "${DESTDIR}${BINDIR}/r2pm"
	rm -f "${DESTDIR}${BINDIR}/ragg2-cc"
	rm -rf "${DESTDIR}${DATADIR}/radare2/${VERSION}/r2pm"
	#mkdir -p "${DESTDIR}${DATADIR}/radare2/${VERSION}/r2pm"
	#cp -rf r2pm/d/* "${DESTDIR}${DATADIR}/radare2/${VERSION}/r2pm"
	${INSTALL_SCRIPT} ragg2/ragg2-cc "${DESTDIR}${BINDIR}/ragg2-cc"
	-cd "${DESTDIR}${BINDIR}" && rm -f r2 ; ln -fs radare2 r2
ifeq ($(WITHPIC),1)
	${INSTALL_LIB} "preload/libr2.$(EXT_SO)" "${DESTDIR}${LIBDIR}"
	-cd "${DESTDIR}${LIBDIR}" && \
		mv libr2.$(EXT_SO) $(LIBR2) && \
		ln -fs $(LIBR2) libr2.$(EXT_SO)
endif

ios_sign ios-sign ios-sdk-sign:
	for a in $(BINS) ; do xcrun --sdk iphoneos codesign -s- --entitlements radare2/radare2.xml $$a ; done

symstall install-symlink:
	cd .. && \
	mkdir -p "${DESTDIR}${BINDIR}" && \
	for BINARY in ${BINS} ; do ln -fs "${BTOP}/$$BINARY/$$BINARY" "${DESTDIR}${BINDIR}/$$BINARY" ; done
	cd .. && ln -fs "${BTOP}/ragg2/ragg2-cc" "${DESTDIR}${BINDIR}/ragg2-cc"
	cd .. && ln -fs "${BTOP}/r2pm/r2pm" "${DESTDIR}${BINDIR}/r2pm"
	cd .. && rm -rf "${DESTDIR}${DATADIR}/radare2/${VERSION}/r2pm"
	cd .. && mkdir -p "${DESTDIR}${DATADIR}/radare2/${VERSION}/"
	#ln -fs "$(BTOP)/r2pm/d" "${DESTDIR}${DATADIR}/radare2/${VERSION}/r2pm"
	-cd .. && ln -fs "${DESTDIR}${BINDIR}/radare2" "${DESTDIR}${BINDIR}/r2"
	-cd .. && ln -fs "${BTOP}/preload/libr2.$(EXT_SO)" "${DESTDIR}${LIBDIR}/libr2.$(EXT_SO)"
	-cd .. && cd "${DESTDIR}${LIBDIR}" && \
		mv libr2.$(EXT_SO) $(LIBR2) && \
		ln -fs $(LIBR2) libr2.$(EXT_SO)

deinstall uninstall:
	-cd .. ; \
	for BINARY in ${BINS} ; do rm -f "${DESTDIR}${BINDIR}/$$BINARY" ; done ; \
	rm -f "${DESTDIR}${BINDIR}/ragg2-cc" ; \
	rm -f "${DESTDIR}${BINDIR}/r2pm" ; \
	rm -f "${DESTDIR}${BINDIR}/r2"

clean:
	@for DIR in preload ${BINS} ; do ${MAKE} -C $$DIR clean; done

mrproper:
	@for DIR in preload ${BINS} ; do ${MAKE} -C $$DIR mrproper; done

