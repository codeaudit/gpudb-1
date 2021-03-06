
OS_SIZE = $(shell uname -m | sed -e "s/i.86/32/" -e "s/x86_64/64/")

CUDA_PATH       ?= /usr/local/cuda
CUDA_INC_PATH   ?= $(CUDA_PATH)/include
CUDA_BIN_PATH   ?= $(CUDA_PATH)/bin

ifeq ($(OS_SIZE),32)
    CUDA_LIB_PATH  ?= $(CUDA_PATH)/lib
else
    CUDA_LIB_PATH  ?= $(CUDA_PATH)/lib64
endif

NVCC            ?= $(CUDA_BIN_PATH)/nvcc

GENCODE_SM20    := -gencode arch=compute_20,code=sm_20
GENCODE_SM30    := -gencode arch=compute_30,code=sm_30 -gencode arch=compute_35,code=sm_35
GENCODE_FLAGS   := $(GENCODE_SM20) $(GENCODE_SM30)

ifeq ($(OS_SIZE),32)
    LDFLAGS   := -lcudart -lrt
else
    LDFLAGS   := -lcudart -lrt
endif


gpudb: gpu cpu
	 $(NVCC) -pg tableScan.o inviJoin.o hashJoin.o cuckoo.o materialize.o groupby.o orderBy.o driver.o $(LDFLAGS) -o GPUDATABASE

cpu: driver.cu 
	$(NVCC) -c -g $(GENCODE_FLAGS) driver.cu

gpu: tableScan.cu hashJoin.cu cuckoo.cu inviJoin.cu materialize.cu groupby.cu orderBy.cu
	$(NVCC) -c -g $(GENCODE_FLAGS) tableScan.cu hashJoin.cu cuckoo.cu inviJoin.cu materialize.cu groupby.cu orderBy.cu 

all: gpudb

clean:
	rm -rf *.o GPUDATABASE
