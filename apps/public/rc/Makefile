
CFLAGS = -Wall -Os $(TC_CFLAGS)
CFLAGS += -I.
CFLAGS += -I../shared -I../utils
LDFLAGS = -L../shared -lshared -L../utils -lutils
ifneq ($(RTCONFIG_USB),)
CFLAGS += -I../libdisk
LDFLAGS += -L../libdisk -ldisk
endif
CFLAGS += -I$(APP_TCAPILIB_DIR)
LDFLAGS += -L$(APP_TCAPILIB_DIR) -ltcapi -lcrypt

CFLAGS += -ffunction-sections -fdata-sections
CFLAGS += -DU2EC
LDFLAGS += -ffunction-sections -fdata-sections -Wl,--gc-sections
ifeq ($(STATIC),1)
LDFLAGS += -static
endif

OBJS := rc.o
OBJS += init.o common.o services.o wan.o ppp.o
OBJS += wanduck.o udhcpc.o interface.o lan.o
OBJS += tcpcheck.o
ifneq ($(RTCONFIG_USB),)
OBJS += usb.o
OBJS += usb_devices.o
endif
OBJS += pppd.o

ifeq ($(RTCONFIG_ACCEL_PPTPD),y)
OBJS += vpn.o
endif

ifeq ($(RTCONFIG_OPENVPN),y)
CFLAGS += -DRTCONFIG_CROND
OBJS += openvpn.o
endif

ifeq ($(RTCONFIG_VPNC),y)
OBJS += vpnc.o
endif

ifeq ($(RTCONFIG_TR069),y)
OBJS += tr069.o
endif

all: rc

rc: $(OBJS)
	@echo " [rc] CC $@"
	$(CC) -o $@ $^ $(LDFLAGS)

	$(SIZECHECK)
	$(CPTMP)

clean:
	rm -f rc *.o .*.depend

size: rc
	mipsel-uclibc-nm --print-size --size-sort rc

%.o: %.c .%.depend
	@echo " [rc] CC $@"
	$(CC) $(CFLAGS) -c $<

.%.depend: %.c
	$(CC) $(CFLAGS) -M $< > $@

-include $(OBJS:%.o=.%.depend)
