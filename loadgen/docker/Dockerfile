# Copyright 2021 Google LLC
#
# Licensed under the Apache License, Version 2.0 (the "License");
# you may not use this file except in compliance with the License.
# You may obtain a copy of the License at
#
#     https://www.apache.org/licenses/LICENSE-2.0
#
# Unless required by applicable law or agreed to in writing, software
# distributed under the License is distributed on an "AS IS" BASIS,
# WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
# See the License for the specific language governing permissions and
# limitations under the License.
FROM ubuntu:18.04

ENV LOADGEN_BRANCH "master"

RUN apt-get update && apt-get install -y --no-install-recommends \
        ca-certificates \
        cmake \
        curl \
        git \
        libglib2.0-dev \
        python3-distutils \
        software-properties-common \
        && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/*

RUN curl -fSsL -O https://bootstrap.pypa.io/get-pip.py && \
    python3 get-pip.py && \
    rm get-pip.py

# Install python 3.7.
RUN add-apt-repository ppa:deadsnakes/ppa && \
    apt-get update && apt-get install -y \
    python3.7 python3.7-dev python3-pip python3.7-venv && \
    rm -rf /var/lib/apt/lists/* && \
    python3.7 -m pip install pip --upgrade && \
    update-alternatives --install /usr/bin/python3 python3 /usr/bin/python3.7 0

# Make python3.7 the default python version
RUN update-alternatives --install /usr/bin/python python /usr/bin/python3.7 0

RUN pip3 --no-cache-dir install \
    absl-py \
    numpy

WORKDIR /mlperf_inference
RUN git clone -b ${LOADGEN_BRANCH} --recurse-submodules https://github.com/mlperf/inference.git && mv inference/* . && rm -rf inference

# Build and install LoadGen Python bindings.
RUN cd loadgen && CFLAGS="-std=c++14 -O3" python setup.py bdist_wheel && pip install --force-reinstall dist/mlperf_loadgen-0.5a0-cp36-cp36m-linux_x86_64.whl
#RUN mkdir loadgen/build/ && cd loadgen/build && cmake .. && cmake --build . && cp libmlperf_loadgen.a /usr/local/bin


CMD ["/bin/bash"]

