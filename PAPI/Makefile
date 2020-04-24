PAPI_ROOT = /home/ubuntu/papi-5.7.0/src/
PAPI_CUDA_ROOT = /usr/local/cuda/

ARCH_CC = 60
ARCH = arch=compute_$(ARCH_CC),code=sm_$(ARCH_CC)

PAPI_NVML_ROOT ?= $(PAPI_CUDA_ROOT)
PAPI_NVML_INC ?= $(PAPI_CUDA_ROOT)/include
PAPI_NVML_LIB ?= $(PAPI_CUDA_ROOT)/lib64/stubs

NVCC = $(PAPI_CUDA_ROOT)/bin/nvcc
NVCFLAGS = -L$(PAPI_NVML_INC) -ccbin=$(CC)
CUDALIBS = -L$(PAPI_NVML_LIB) -L$(PAPI_CUDA_ROOT)/lib64 -lcuda -lcudart -lnvidia-ml -lstdc++
#PAPILIB := $(PAPI_ROOT)/lib/libpapi.a  -ldl
PAPILIB := $(PAPI_ROOT)/libpapi.a  -ldl
INCLUDE := -I$(PAPI_ROOT)/include -I$(PAPI_ROOT)
NONOPT := -Xptxas -O0

nonOpt: pipeline.cu $(UTILOBJS)
	@echo "Compiling Non Optimized Version"
	@$(NVCC) $(NVCFLAGS) $(INCLUDE) --keep -gencode $(ARCH) pipeline.cu $(UTILOBJS) $(NONOPT) $(PAPILIB) $(CUDALIBS)
	@$(NVCC) $(NVCFLAGS) $(INCLUDE) -dryrun -gencode $(ARCH) pipeline.cu $(UTILOBJS) $(NONOPT) $(PAPILIB) $(CUDALIBS) --keep 2>dryrun.out
	@rm pipeline.ptx
	@cp nonOpt_pipeline_temp.ptx pipeline.ptx
	@sed -i "s/^..//g" dryrun.out
	@tail -n 9 dryrun.out >dryrun.out.temp
	@mv dryrun.out.temp dryrun.out
	@chmod +x dryrun.out
	@./dryrun.out

opt: pipeline.cu $(UTILOBJS)
	@echo "Compiling Optimized Version"
	@$(NVCC) $(NVCFLAGS) $(INCLUDE) --keep -gencode $(ARCH) pipeline.cu $(UTILOBJS) $(PAPILIB) $(CUDALIBS)
	@$(NVCC) $(NVCFLAGS) $(INCLUDE) -dryrun -gencode $(ARCH) pipeline.cu $(UTILOBJS) $(PAPILIB) $(CUDALIBS) --keep 2>dryrun.out
	@rm pipeline.ptx
	@cp opt_pipeline_temp.ptx pipeline.ptx
	@sed -i "s/^..//g" dryrun.out
	@tail -n 9 dryrun.out >dryrun.out.temp
	@mv dryrun.out.temp dryrun.out
	@chmod +x dryrun.out
	@./dryrun.out

run: $(type)
	@mkdir -p output
	@./run.sh

clean:
	rm -f $(TESTS) *.o
	rm -f benchSANVML
	rm -f nvmlcap_plot results*
	rm -f a_dlink* pipeline.cpp* pipeline.cubin* pipeline.cudafe* pipeline.fatbin* pipeline.module_id pipeline.ptx
	rm -f a.out dryrun*
	rm -rf output/*
