LIBRARY	= mtev.so
MODULES = src

o_src	= caps \
	hw \
	mtouch \
	multitouch

#TARGETS	= $(addsuffix /test,$(MODULES))

OBJECTS	= $(addsuffix .o,\
	$(foreach mod,$(MODULES),\
	$(addprefix $(mod)/,$(o_$(mod)))))

#TBIN	= $(addprefix bin/,$(TARGETS))
TLIB	= $(addprefix obj/,$(LIBRARY))
#TOBJ	= $(addprefix obj/,$(addsuffix .o,$(TARGETS)))
#TFDI	= $(addprefix fdi/,$(FDIS))
OBJS	= $(addprefix obj/,$(OBJECTS))
#LIBS	= -lpixman-1

DLIB	= usr/lib/xorg/modules/input
# DFDI	= usr/share/hal/fdi/policy/20thirdparty

#INCLUDE = -I/usr/include/xorg -I/usr/include/pixman-1
OPTS	= $(CFLAGS) -fPIC 

.PHONY: all clean
.PRECIOUS: obj/%.o

VERSION=$(shell cat debian/changelog | head -n 1 | sed -e 's/.*(\(.*\)).*/\1/g')

all:	$(OBJS) $(TLIB) $(TOBJ)
# $(TBIN)

bin/%:	obj/%.o
	@mkdir -p $(@D)
	$(CC) $< -o $@

$(TLIB): $(OBJS)
	@rm -f $(TLIB)
	$(CC) -shared $(OBJS) -Wl,-soname -Wl,$(LIBRARY) $(CFLAGS) -o $@

obj/%.o: %.c
	@mkdir -p $(@D)
	$(CC) $(INCLUDE) $(OPTS) -c $< -o $@

obj/%.o: %.cc
	@mkdir -p $(@D)
	$(CC) $(INCLUDE) $(OPTS) -c $< -o $@

clean:
	rm -rf bin obj

spec: xf86-input-mtev.yaml
	specify xf86-input-mtev.yaml

dist: spec
	git archive --format=tar --prefix=xf86-input-mtev-$(VERSION)/ maemo/$(VERSION)-1 | gzip >xf86-input-mtev-$(VERSION).tar.gz

rpm: dist
#	cp xf86-input-mtev-$(VERSION).tar.gz rpm/SOURCES/
#	rpmbuild -v -bb --clean xf86-input-mtev.spec

# doesnt work :(
	sudo build --repository http://repo.meego.com/MeeGo/releases/1.0.1/core/repos/ia32/packages --arch i686 xf86-input-mtev.spec

#http://repo.meego.com/MeeGo/releases/1.0.1/core/repos/ia32/os/ --arch i686 xf86-input-mtev.spec

distclean: clean
	rm -rf debian/*.log debian/files

install: $(TLIB) $(TFDI)
	install -d "$(DESTDIR)/$(DLIB)"
	install -m 755 $(TLIB) "$(DESTDIR)/$(DLIB)"
