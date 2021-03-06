include ./Makefile.inc

PROG	= rtosdemo
DEBUG 	= -g

RTOS_SOURCE_DIR	= ../../Source

RTOS_SRC = \
	$(RTOS_SOURCE_DIR)/croutine.c \
	$(RTOS_SOURCE_DIR)/list.c \
	$(RTOS_SOURCE_DIR)/queue.c \
	$(RTOS_SOURCE_DIR)/tasks.c \
	$(RTOS_SOURCE_DIR)/portable/MemMang/heap_2.c 

APP_SOURCE_DIR	= ../Common/Minimal

APP_SRC = \
	$(APP_SOURCE_DIR)/dynamic.c \
	$(APP_SOURCE_DIR)/integer.c \
	$(APP_SOURCE_DIR)/blocktim.c \
	$(APP_SOURCE_DIR)/BlockQ.c \
	$(APP_SOURCE_DIR)/flash.c \
	$(APP_SOURCE_DIR)/comtest.c 

PORT_SRC = $(RTOS_SOURCE_DIR)/portable/GCC/OpenRISC/port.c
PORT_ASM = $(RTOS_SOURCE_DIR)/portable/GCC/OpenRISC/portasm.S

DEMO_SRC = main.c serial/serial.c ParTest/ParTest.c dma/or32_dma.c
ARCH_ASM = ./arch/reset.S

INC		= -I. -I../../Source/include -I./arch -I./drivers -I./dma \
		  -I../Common/include \
		  -I$(RTOS_SOURCE_DIR)/portable/GCC/OpenRISC

CFLAGS	+= $(WARNINGS) $(INC) $(DEBUG) \
		-D__GCC_OpenRISC__ \
		-fomit-frame-pointer -fno-strict-aliasing -fno-builtin

GCCVER 	= $(shell $(CC) --version | grep gcc | cut -d" " -f9)
CCPATH	= /home/dmw/works/or32/tools/or32-new

#
# Define all object files.
#
RTOS_OBJ = $(RTOS_SRC:.c=.o)
APP_OBJ  = $(APP_SRC:.c=.o)
PORT_OBJ = $(PORT_SRC:.c=.o)
DEMO_OBJ = $(DEMO_SRC:.c=.o)
PORT_ASM_OBJ = $(PORT_ASM:.S=.o)
ARCH_ASM_OBJ = $(ARCH_ASM:.S=.o)

LDFLAGS	 = -T ./arch/link.ld -Map $(PROG).map
LIBS	 = -L./arch -larch -L./drivers -ldrivers \
		   -L$(CCPATH)/lib/gcc/$(TARGET)/$(GCCVER) \
		   -L$(CCPATH)/$(TARGET)/lib \
		   -lgcc -lc -lm

%.o: %.c
	@echo "    CC $<"
	@$(CC) -c $(CFLAGS) -o $@ $<

%.o: %.S
	@echo "    ASM $<"
	@$(CC) -c $(CFLAGS) -o $@ $<

all: $(PROG).or32
	@$(SIZE) $(PORT_ASM_OBJ) $(PORT_OBJ) $(RTOS_OBJ) $(DEMO_OBJ) $(APP_OBJ) $(PROG).or32

$(PROG).or32 : $(PORT_ASM_OBJ) $(PORT_OBJ) $(RTOS_OBJ) $(DEMO_OBJ) $(APP_OBJ) Makefile arch drivers
	@echo Link....
	@$(LD) -o $@ $(LDFLAGS) --start-group $(ARCH_ASM_OBJ) $(PORT_ASM_OBJ) $(PORT_OBJ) $(RTOS_OBJ) $(DEMO_OBJ) $(APP_OBJ) $(LIBS) --end-group 
	@$(OBJDUMP) -S $(PROG).or32 >> $(PROG).asm	
	@echo Complete $@

arch: force_true
	@(cd $@; make)

drivers: force_true
	@(cd $@; make)

clean :
	@rm -f $(RTOS_OBJ) $(DEMO_OBJ) $(PORT_OBJ) $(PORT_ASM_OBJ)
	@rm -f rtosdemo.or32 
	@rm -f rtosdemo.map
	@rm -f rtosdemo.asm
	@rm -f eth0.tx
	@(cd arch;make --no-print-directory clean)
	@(cd drivers;make --no-print-directory clean)

force_true:
	@true

#-------------------------------------------------------------
sim: $(PROG).or32
	or32-elf-sim --nosrv -f sim.cfg $(PROG).or32

debug: $(PROG).or32
	xterm -e or32-elf-sim -f sim.cfg --srv=9999 &
	xterm -e or32-elf-gdb $(PROG).or32 &


