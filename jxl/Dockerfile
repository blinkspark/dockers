FROM ubuntu:20.04 as builder
ENV HTTP_PROXY=http://192.168.3.219:22331 HTTPS_PROXY=http://192.168.3.219:22331
RUN apt update && apt install -y ca-certificates
COPY sources.list /etc/apt/sources.list
ENV TZ=Asia/Shanghai
ENV DEBIAN_FRONTEND=noninteractive
RUN apt update && apt install -y \
    cmake pkg-config libbrotli-dev \
    libgif-dev libjpeg-dev libopenexr-dev libpng-dev libwebp-dev \
    clang git
ENV CC=clang
ENV CXX=clang++
WORKDIR /
RUN git clone https://github.com/libjxl/libjxl.git --recursive
WORKDIR /libjxl/build
RUN cmake -DCMAKE_BUILD_TYPE=Release -DBUILD_TESTING=OFF ..
RUN cmake --build . -- -j$(nproc)
# RUN cmake --install .
# ENV DEBIAN_FRONTEND=dialog

FROM ubuntu:20.04 as runner
ENV HTTP_PROXY=http://192.168.3.219:22331 HTTPS_PROXY=http://192.168.3.219:22331
RUN apt update && apt install -y ca-certificates \
 libgif-dev libjpeg-dev libopenexr-dev libpng-dev libwebp-dev
COPY --from=builder /libjxl /libjxl
WORKDIR /libjxl
ENV HTTP_PROXY="" HTTPS_PROXY=""
RUN apt autoremove -y && apt clean -y
RUN ln -s /libjxl/build/tools/cjxl /usr/local/bin/cjxl \
    && ln -s /libjxl/build/tools/djxl /usr/local/bin/djxl