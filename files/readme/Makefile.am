EXTRA_DIST = data/readme.in data/readme.desktop data/readme.man data/mandoc.css
CLEANFILES = data/readme data/readme.html
applicationsdir = $(datadir)/applications
applications_DATA = data/readme.desktop
pkgdata_DATA = data/readme.man
bin_SCRIPTS = data/readme
html_DATA = data/readme.html data/mandoc.css
do_subst = \
	sed -e 's,[@]pkgdatadir[@],$(pkgdatadir),g'
data/readme.html: data/readme.man
	@mkdir -p data
	mandoc -T html -O style=mandoc.css $< > $@
data/readme: data/readme.in
	@mkdir -p data
	$(do_subst) < $< > $@
bindist: dist
	tar xf $(distdir).tar.gz
	mkdir $(distdir)/_build $(distdir)/_inst
	inst=`cd $(distdir)/_inst && pwd` \
	  && cd $(distdir)/_build \
	  && ../configure --prefix=/usr/local \
	  && make DESTDIR="$$inst" install
	tar --sort=name --owner=0 --group=0 --numeric-owner \
	  -cvzf $(distdir)-bin.tar.gz -C $(distdir)/_inst .
	rm -r $(distdir)
