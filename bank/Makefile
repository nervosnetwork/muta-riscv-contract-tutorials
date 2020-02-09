TARGET := riscv64-unknown-elf
CC := $(TARGET)-gcc
LD := $(TARGET)-gcc
CFLAGS := -Os -DCKB_NO_MMU -D__riscv_soft_float -D__riscv_float_abi_soft
LDFLAGS := -lm -Wl,-static -fdata-sections -ffunction-sections -Wl,--gc-sections -Wl,-s
CURRENT_DIR := $(shell pwd)
DOCKER_BUILD := docker run --rm -it -v $(CURRENT_DIR):/src nervos/ckb-riscv-gnu-toolchain:xenial bash -c
DEPS := $(CURRENT_DIR)/deps
MID := $(CURRENT_DIR)/middle
BIN := $(CURRENT_DIR)/bin

libpvm.a:
	$(CC) -I$(DEPS) -c $(DEPS)/pvm.c -o $(MID)/pvm.o
	$(CC) -I$(DEPS) -c $(DEPS)/UsefulBuf.c -o $(MID)/UsefulBuf.o
	$(CC) -I$(DEPS) -c $(DEPS)/pvm_structs.c -o $(MID)/pvm_structs.o
	ar rcs $(MID)/libpvm.a $(MID)/UsefulBuf.o $(MID)/pvm_structs.o $(MID)/pvm.o

bin: libpvm.a
	$(CC) -I$(DEPS) $(DEPS)/cJSON.h $(DEPS)/cJSON.c erc20.c $(MID)/libpvm.a $(LDFLAGS) -o $(BIN)/erc20.bin
	$(CC) -I$(DEPS) $(DEPS)/cJSON.h $(DEPS)/cJSON.c bank.c $(MID)/libpvm.a $(LDFLAGS) -o $(BIN)/bank.bin

bin_docker:
	$(DOCKER_BUILD) "cd /src && make bin"

clear:
	@rm -rf middle/*