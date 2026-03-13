CFLAGS = -g -O2 -DRTAPI -D_GNU_SOURCE -I$(STAGING_DIR)/usr/include/linuxcnc -I. -I../asm
INSTALL = install -m 0644
DESTDIR = $(TARGET_DIR)/usr/lib/linuxcnc/modules

all: hal_modules

install: hal_pru_generic.so
	$(INSTALL) -t $(DESTDIR) $<

hal_modules: hal_pru_generic.so

hal_pru_generic.so: hal_pru_generic.o stepgen.o encoder.o pwmgen.o

%.so:
	echo Linking $@
	$(LD) -d -r -o $*.tmp $^
	$(OBJCOPY) -j .rtapi_export -O binary $*.tmp $*.sym
	(echo '{ global : '; tr -s '\0' < $*.sym | xargs -r0 printf '%s;\n' | grep .; echo 'local : * ; };') > $*.ver
	$(CC) -shared -Bsymbolic $(LDFLAGS) -Wl,--version-script,$*.ver -o $@ $^ -lm 

.c.o:
	echo Compiling realtime $<
	$(CC) -c $(CFLAGS) $< -o $@

clean:
	rm -f *.o *.tmp *.sym *.ver *.so
