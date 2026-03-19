all: hal_modules

install: hal_pru_generic.so
	install -m 0644 -t $(TARGET_DIR)/usr/lib/linuxcnc/modules $<

hal_modules: hal_pru_generic.so

hal_pru_generic.so: hal_pru_generic.o stepgen.o encoder.o pwmgen.o

%.so:
	echo Linking $@
	$(LD) -d -r -o $*.tmp $^
	$(OBJDUMP) -w -j .rtapi_export -t $*.tmp \
	| awk 'BEGIN{print "{ global :"} /rtapi_exported_/{printf("%s;\n", substr($$6,16))} END{print "local : * ; };"}' \
	> $*.ver
	$(CC) -shared -Bsymbolic -Wl,--version-script,$*.ver -o $@ $^ -lm

%.o: %.c
	echo Compiling realtime $<
	$(CC) -o $@ -Os -g -I$(STAGING_DIR)/usr/include/linuxcnc -I. -I../asm -DRTAPI -fno-fast-math  -fno-unsafe-math-optimizations -DRTAPI -D_GNU_SOURCE -Drealtime -D__MODULE__ -Wframe-larger-than=2560 -c $<

clean:
	rm *.o
