#------------------------------------------------------------------------#
# Output files
ELF_FILE	=	dolphin-os.elf
BIN_FILE	=	dolphin-os.bin
HEX_FILE	=	dolphin-os.hex
INFO_FILE	=	dolphin-os.info
CODE_FILE	=	dolphin-os.code

#------------------------------------------------------------------------#
# Cross Compiler
CC			=	arm-none-eabi-gcc
OBJCOPY		=	arm-none-eabi-objcopy
OBJDUMP		=	arm-none-eabi-objdump
READELF		=	arm-none-eabi-readelf

#------------------------------------------------------------------------#
# Flags
CFLAGS		+=	-g
CFLAGS		+=	-mthumb
CFLAGS		+=	-Wno-incompatible-pointer-types
CFLAGS		+=	-Wno-unused-but-set-variable
CFLAGS		+=	-Wno-unused-variable
CFLAGS		+=	-mcpu=cortex-m3
CFLAGS		+=	-I./
CFLAGS		+=	-D"assert_param(expr)=((void)0)"
CFLAGS		+=	-D STM32F10X_MD
CFLAGS		+=	-nostartfiles

#------------------------------------------------------------------------#
# Link script
CFLAGS		+=	-Wl,-Tstm32_flash.ld

#------------------------------------------------------------------------#
# Libraries
STM32_LIBS	=	libs/STM32_USB-FS-Device_Lib_V4.0.0/Libraries/STM32F10x_StdPeriph_Driver
CFLAGS		+=	-I$(STM32_LIBS)/inc
CFLAGS		+=	-Ilibs/STM32_USB-FS-Device_Lib_V4.0.0/Libraries/CMSIS/Include
CFLAGS		+=	-Ilibs/STM32_USB-FS-Device_Lib_V4.0.0/Libraries/CMSIS/Device/ST/STM32F10x/Include

KERNEL_PATH	=	../../src
CFLAGS		+=	-I./src/include
CFLAGS		+=	-I./src/main
CFLAGS		+=	-I./src/hardware
CFLAGS		+=	-I./src/modules
CFLAGS		+=	-I$(KERNEL_PATH)/kernel
CFLAGS		+=	-I$(KERNEL_PATH)/lib

#------------------------------------------------------------------------#
# StdPeriph
SRC			=	$(STM32_LIBS)/src/misc.c \
				$(STM32_LIBS)/src/stm32f10x_rcc.c \
				$(STM32_LIBS)/src/stm32f10x_gpio.c \
				$(STM32_LIBS)/src/stm32f10x_tim.c \
				$(STM32_LIBS)/src/stm32f10x_usart.c \
				$(STM32_LIBS)/src/stm32f10x_exti.c \
				$(STM32_LIBS)/src/stm32f10x_i2c.c

#------------------------------------------------------------------------#
# Setup system clock
SRC			+=	./src/main/system_stm32f10x.c

#------------------------------------------------------------------------#
# Main function
SRC			+=	./src/main/main.c \
				./src/main/task.c

#------------------------------------------------------------------------#
# Modules
SRC			+=	./src/hardware/led.c \
				./src/hardware/sysclk.c \
				./src/hardware/tim2.c \
				./src/hardware/tim3.c \
				./src/hardware/uart1.c \
				./src/modules/protocol.c \
				./src/modules/hcsr04.c \
				./src/modules/pwmout.c

SRC			+=	$(KERNEL_PATH)/kernel/core.c \
				$(KERNEL_PATH)/kernel/pcb.c \
				$(KERNEL_PATH)/kernel/sche.c \
				$(KERNEL_PATH)/kernel/sem.c \
				$(KERNEL_PATH)/kernel/switch.S \
				$(KERNEL_PATH)/kernel/mm.c \
				$(KERNEL_PATH)/lib/slist.c

STARTUP		=	./src/main/startup_stm32f10x_md.s

STARTUP_OBJ	=	startup_stm32f10x_md.o

all:$(BIN_FILE) $(HEX_FILE) $(INFO_FILE) $(CODE_FILE)

$(BIN_FILE):$(ELF_FILE)
	$(OBJCOPY) -O binary $^ $@

$(HEX_FILE):$(ELF_FILE)
	$(OBJCOPY) -O ihex $^ $@

$(INFO_FILE):$(ELF_FILE)
	$(READELF) -a $^ > $@

$(CODE_FILE):$(ELF_FILE)
	$(OBJDUMP) -S $^ > $@

$(STARTUP_OBJ):$(STARTUP)
	$(CC) $(CFLAGS) $^ -c $(STARTUP)

$(ELF_FILE):$(SRC) $(STARTUP_OBJ)
	$(CC) $(CFLAGS)	$^ -o $@

install:
	jlink -commanderscript loadbin_cmd.jlink

clean:
	@rm -rvf *.o *.bin *.code *.elf *.hex *.info
