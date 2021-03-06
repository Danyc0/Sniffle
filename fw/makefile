CC = "$(GCC_ARMCOMPILER)/bin/arm-none-eabi-gcc"
LNK = "$(GCC_ARMCOMPILER)/bin/arm-none-eabi-gcc"

CONFIGPKG = rtos_build
NAME = sniffle

CC26X2_PLATFORMS = CC2642R1F CC2652R1F
CC1352_PLATFORMS = CC1352R1F3
CC2652RB_PLATFORMS = CC2652RB1F
CC2640R2_PLATFORMS = CC2640R2F

SDK_CC13x2_26x2 = $(CC26X2_PLATFORMS) $(CC1352_PLATFORMS) $(CC2652RB_PLATFORMS) 
SDK_CC2640r2 = $(CC2640R2_PLATFORMS)
SUPPORTED_PLATFORMS = $(SDK_CC13x2_26x2) $(SDK_CC2640r2)

ifeq ($(PLATFORM),)
    PLATFORM = CC2642R1F
endif

ifeq ($(filter $(PLATFORM), $(SUPPORTED_PLATFORMS)),)
    $(error "PLATFORM not in $(SUPPORTED_PLATFORMS)")
endif

ifneq ($(filter $(PLATFORM), $(CC26X2_PLATFORMS)),)
    SOURCES = $(wildcard CC26X2/*.c)
    CCXML = CC26X2/CC2652R1F.ccxml
    CFLAGS += -DDeviceFamily_CC26X2
    CFLAGS += -ICC26X2
    LFLAGS += -Wl,-T,CC26X2/CC26X2R1_LAUNCHXL_TIRTOS.lds
    LFLAGS += -l:ti/drivers/rf/lib/rf_multiMode_cc26x2.am4fg
    LFLAGS += -l:ti/drivers/lib/drivers_cc26x2.am4fg
    LFLAGS += -l:ti/dpl/lib/dpl_cc26x2.am4fg
    SYSCFG_BOARD = /ti/boards/CC26X2R1_LAUNCHXL
endif
ifneq ($(filter $(PLATFORM), $(CC1352_PLATFORMS)),)
    SOURCES = $(wildcard CC1352/*.c)
    CCXML = CC1352/CC1352R1F3.ccxml
    CFLAGS += -DDeviceFamily_CC13X2
    CFLAGS += -ICC1352
    LFLAGS += -Wl,-T,CC1352/CC1352R1_LAUNCHXL_TIRTOS.lds
    LFLAGS += -l:ti/drivers/rf/lib/rf_multiMode_cc13x2.am4fg
    LFLAGS += -l:ti/drivers/lib/drivers_cc13x2.am4fg
    LFLAGS += -l:ti/dpl/lib/dpl_cc13x2.am4fg
    SYSCFG_BOARD = /ti/boards/CC1352R1_LAUNCHXL
endif
ifneq ($(filter $(PLATFORM), $(CC2652RB_PLATFORMS)),)
    SOURCES = $(wildcard CC2652RB/*.c)
    CCXML = CC2652RB/CC2652RB1F.ccxml
    CFLAGS += -DDeviceFamily_CC26X2
    CFLAGS += -ICC26X2
    LFLAGS += -Wl,-T,CC2652RB/CC2652RB_LAUNCHXL_TIRTOS.lds
    LFLAGS += -l:ti/drivers/rf/lib/rf_multiMode_cc26x2.am4fg
    LFLAGS += -l:ti/drivers/lib/drivers_cc26x2.am4fg
    LFLAGS += -l:ti/dpl/lib/dpl_cc26x2.am4fg
    SYSCFG_BOARD = /ti/boards/CC2652RB_LAUNCHXL
endif
ifneq ($(filter $(PLATFORM), $(CC2640R2_PLATFORMS)),)
    SOURCES = $(wildcard CC2640R2/*.c)
    CCXML = CC2640R2/CC2640R2F.ccxml
    CFLAGS += -DDeviceFamily_CC26X0R2
    CFLAGS += -ICC2640
    LFLAGS += -Wl,-T,CC2640R2/CC2640R2_LAUNCHXL_TIRTOS.lds
    LFLAGS += -l:ti/drivers/rf/lib/rf_multiMode_cc26x0r2.am3g
    LFLAGS += -l:ti/drivers/lib/drivers_cc26x0r2.am3g
    LFLAGS += -l:ti/dpl/lib/dpl_cc26x0r2.am3g
    SYSCFG_BOARD = /ti/boards/CC2640R2_LAUNCHXL
endif

TI_PLTFRM = ti.platforms.simplelink:$(PLATFORM)

ifneq ($(filter $(PLATFORM), $(SDK_CC13x2_26x2)),)
    SIMPLELINK_SDK_INSTALL_DIR ?= $(HOME)/ti/simplelink_cc13x2_26x2_sdk_3_40_00_02
    XDCTARGET = gnu.targets.arm.M4F
    LFLAGS += \
        -l:ti/display/lib/display.am4fg \
        -l:ti/grlib/lib/gcc/m4f/grlib.a \
        -l:third_party/spiffs/lib/gcc/m4f/spiffs_cc26xx.a
else
    SIMPLELINK_SDK_INSTALL_DIR ?= $(HOME)/ti/simplelink_cc2640r2_sdk_3_40_00_10
    XDCTARGET = gnu.targets.arm.M3
    LFLAGS += \
        -l:ti/display/lib/display.am3g \
        -l:ti/grlib/lib/gcc/m3/grlib.a \
        -l:third_party/spiffs/lib/gcc/m3/spiffs_cc26xx.a
endif

include $(SIMPLELINK_SDK_INSTALL_DIR)/imports.mak

XDCPATH = $(SIMPLELINK_SDK_INSTALL_DIR)/source;$(SIMPLELINK_SDK_INSTALL_DIR)/kernel/tirtos/packages;

CFLAGS += -I. \
    -D__STRICT_ANSI__ \
    "-I$(SIMPLELINK_SDK_INSTALL_DIR)/source/ti/posix/gcc" \
    -std=c99 \
    -ffunction-sections \
    -fdata-sections \
    -gstrict-dwarf \
    -Wall

LFLAGS += \
    "-Wl,-Map,$(NAME).map" \
    "-L$(SIMPLELINK_SDK_INSTALL_DIR)/source" \
    "-L$(SIMPLELINK_SDK_INSTALL_DIR)/kernel/tirtos/packages" \
    "-Wl,-T,$(CONFIGPKG)/linker.cmd" \
    -march=armv7e-m \
    -mthumb \
    -mfpu=fpv4-sp-d16 \
    -nostartfiles \
    -static \
    -Wl,--gc-sections \
    "-L$(GCC_ARMCOMPILER)/arm-none-eabi/lib" \
    -lgcc \
    -lc \
    -lm \
    -lnosys \
    --specs=nano.specs

ifneq ($(filter $(PLATFORM), $(SDK_CC13x2_26x2)),)
    LFLAGS += \
        -mfloat-abi=hard \
        "-L$(SIMPLELINK_SDK_INSTALL_DIR)/kernel/tirtos/packages/gnu/targets/arm/libs/install-native/arm-none-eabi/lib/thumb/v7e-m/fpv4-sp/hard" \
        -l:ti/devices/cc13x2_cc26x2/driverlib/bin/gcc/driverlib.lib
else
    LFLAGS += \
        -mfloat-abi=softfp \
        "-L$(SIMPLELINK_SDK_INSTALL_DIR)/kernel/tirtos/packages/gnu/targets/arm/libs/install-native/arm-none-eabi/lib/thumb/v7e-m/" \
        -l:ti/devices/cc26x0r2/driverlib/bin/gcc/driverlib.lib
endif

# SysConfig generated
SOURCES += ti_drivers_config.c ti_devices_config.c ti_radio_config.c

# Sniffle Code
SOURCES += \
    adv_header_cache.c \
    AuxAdvScheduler.c \
    base64.c \
    CommandTask.c \
    conf_queue.c \
    csa2.c \
    debug.c \
    DelayHopTrigger.c \
    DelayStopTrigger.c \
    main.c \
    messenger.c \
    PacketTask.c \
    RadioTask.c \
    RadioWrapper.c \
    RFQueue.c

OBJECTS = $(patsubst %.c,%.obj,$(SOURCES))

.PRECIOUS: $(CONFIGPKG)/compiler.opt $(CONFIGPKG)/linker.cmd

all: $(NAME).out

$(CONFIGPKG)/compiler.opt: $(CONFIGPKG)/linker.cmd

$(CONFIGPKG)/linker.cmd: sniffle.cfg
	@ echo Running Configuro...
	@ $(XDC_INSTALL_DIR)/xs --xdcpath="$(XDCPATH)" xdc.tools.configuro -c "$(GCC_ARMCOMPILER)" \
        -t $(XDCTARGET) -p $(TI_PLTFRM) --compileOptions "$(CFLAGS)" -o "$(CONFIGPKG)" $<

.INTERMEDIATE: syscfg
ti_radio_config.c ti_drivers_config.c ti_devices_config.c ti_radio_config.h ti_drivers_config.h: syscfg

syscfg: sniffle.syscfg
	@ echo Generating configuration files...
	@ $(SYSCONFIG_TOOL) --board $(SYSCFG_BOARD) --product $(SIMPLELINK_SDK_INSTALL_DIR)/.metadata/product.json --output $(@D) $<

%.obj: %.c $(CONFIGPKG)/compiler.opt
	@ echo Building $@
	@ $(CC) $(CFLAGS) $< -c @$(CONFIGPKG)/compiler.opt -o $@

$(NAME).out: $(OBJECTS) $(CONFIGPKG)/linker.cmd
	@ echo linking...
	@ $(LNK)  $(OBJECTS) $(LFLAGS) -o $(NAME).out

.PHONY: load clean

clean:
	@ echo Cleaning...
	@ $(RM) $(OBJECTS) > $(DEVNULL) 2>&1
	@ $(RM) $(NAME).out > $(DEVNULL) 2>&1
	@ $(RM) $(NAME).map > $(DEVNULL) 2>&1
	@ $(RM) -r $(CONFIGPKG) > $(DEVNULL) 2>&1
	@ $(RM) ti_radio_config.c ti_drivers_config.c ti_devices_config.c ti_radio_config.h ti_drivers_config.h syscfg_c.rov.xs > $(DEVNULL) 2>&1

load: $(NAME).out
	@ echo Flashing...
	@ DSLite load -f $< -c $(CCXML)
