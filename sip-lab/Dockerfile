FROM ubuntu:20.04

ENV DEBIAN_FRONTEND=noninteractive

RUN apt update && \
    apt install -y sngrep iproute2 iputils-ping net-tools && \
    apt clean

CMD ["sh", "-c", "sleep infinity"]

