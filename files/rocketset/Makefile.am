EXTRA_DIST = $(bin_SCRIPTS)
bin_SCRIPTS = data/rocket-setlang data/rocket-setdpi data/osu-xinput-mode
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
