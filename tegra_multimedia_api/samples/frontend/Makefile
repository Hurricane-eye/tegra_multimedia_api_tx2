###############################################################################
#
# Copyright (c) 2016, NVIDIA CORPORATION. All rights reserved.
#
# Redistribution and use in source and binary forms, with or without
# modification, are permitted provided that the following conditions
# are met:
#  * Redistributions of source code must retain the above copyright
#    notice, this list of conditions and the following disclaimer.
#  * Redistributions in binary form must reproduce the above copyright
#    notice, this list of conditions and the following disclaimer in the
#    documentation and/or other materials provided with the distribution.
#  * Neither the name of NVIDIA CORPORATION nor the names of its
#    contributors may be used to endorse or promote products derived
#    from this software without specific prior written permission.
#
# THIS SOFTWARE IS PROVIDED BY THE COPYRIGHT HOLDERS ``AS IS'' AND ANY
# EXPRESS OR IMPLIED WARRANTIES, INCLUDING, BUT NOT LIMITED TO, THE
# IMPLIED WARRANTIES OF MERCHANTABILITY AND FITNESS FOR A PARTICULAR
# PURPOSE ARE DISCLAIMED.  IN NO EVENT SHALL THE COPYRIGHT OWNER OR
# CONTRIBUTORS BE LIABLE FOR ANY DIRECT, INDIRECT, INCIDENTAL, SPECIAL,
# EXEMPLARY, OR CONSEQUENTIAL DAMAGES (INCLUDING, BUT NOT LIMITED TO,
# PROCUREMENT OF SUBSTITUTE GOODS OR SERVICES; LOSS OF USE, DATA, OR
# PROFITS; OR BUSINESS INTERRUPTION) HOWEVER CAUSED AND ON ANY THEORY
# OF LIABILITY, WHETHER IN CONTRACT, STRICT LIABILITY, OR TORT
# (INCLUDING NEGLIGENCE OR OTHERWISE) ARISING IN ANY WAY OUT OF THE USE
# OF THIS SOFTWARE, EVEN IF ADVISED OF THE POSSIBILITY OF SUCH DAMAGE.
#
###############################################################################

include ../Rules.mk

APP := frontend

CLASS_DIR := ../common/classes
ARGUS_UTILS_DIR := ../../argus/samples/utils
ALGO_DIR := ../common/algorithm/cuda
GIE_DIR  := ../common/algorithm/gie

ENABLE_GIE ?= 1

SRCS := \
	main.cpp \
	StreamConsumer.cpp \
	VideoEncodeStreamConsumer.cpp \
	VideoEncoder.cpp \
	$(wildcard $(CLASS_DIR)/*.cpp) \
	$(ARGUS_UTILS_DIR)/Thread.cpp

ifeq ($(ENABLE_GIE), 1)
SRCS += \
	GIEStreamConsumer.cpp
endif

OBJS := $(SRCS:.cpp=.o)

ifeq ($(ENABLE_GIE), 1)
OBJS += \
	../common/algorithm/cuda/NvAnalysis.o \
	../common/algorithm/cuda/NvCudaProc.o \
	../common/algorithm/gie/gie_inference.o
endif

CPPFLAGS += \
	-std=c++11

CPPFLAGS += \
	-I"$(ARGUS_UTILS_DIR)" \
	-I"../../include/libjpeg-8b"

LDFLAGS += \
	-lpthread \
	-lv4l2 -lEGL -lGLESv2 -lX11 \
	-lnvbuf_utils -lnvjpeg -largus

ifeq ($(ENABLE_GIE), 1)
CPPFLAGS += \
	-DENABLE_GIE \
	-I"../common/algorithm/gie" \
	-I"../common/algorithm/cuda" \
	-I"$(CUDA_PATH)/include"

LDFLAGS += \
	-L"$(CUDA_PATH)/targets/aarch64-linux/lib" \
	-lnvinfer -lnvcaffe_parser \
	-lcudart -lcuda -lnvosd \
	-lopencv_objdetect

endif

all: $(APP)

$(CLASS_DIR)/%.o: $(CLASS_DIR)/%.cpp
	$(MAKE) -C $(CLASS_DIR)

$(ALGO_DIR)/%.o: $(ALGO_DIR)/%.c
	$(MAKE) -C $(ALGO_DIR)

$(ALGO_DIR)/%.o: $(ALGO_DIR)/%.cu
	$(MAKE) -C $(ALGO_DIR)

$(GIE_DIR)/%.o: $(GIE_DIR)/%.cpp
	$(MAKE) -C $(GIE_DIR)

%.o: %.cpp
	$(CPP) $(CPPFLAGS) -c $< -o $@

$(APP): $(OBJS)
	$(CPP) -o $@ $(OBJS) $(CPPFLAGS) $(LDFLAGS)

clean:
	$(MAKE) -C $(CLASS_DIR) clean
	$(MAKE) -C $(ALGO_DIR) clean
	$(MAKE) -C $(GIE_DIR) clean

	rm -rf *.o $(APP)
