FROM rust:buster as libmbus
WORKDIR /home/build/builds
RUN git clone https://github.com/rscada/libmbus
WORKDIR /home/build/builds/libmbus
ENV LDFLAGS=-static
RUN ./build.sh

FROM rust:buster as mbus-httpd
WORKDIR /home/build/builds
RUN git clone https://github.com/packom/mbus-httpd
WORKDIR /home/build/builds/mbus-httpd
RUN cargo build

FROM rust:buster
WORKDIR /
COPY --from=libmbus /home/build/builds/libmbus/bin/mbus-serial-scan .
COPY --from=libmbus /home/build/builds/libmbus/bin/mbus-serial-request-data .
COPY --from=libmbus /home/build/builds/libmbus/bin/mbus-serial-request-data-multi-reply .
COPY --from=mbus-httpd /home/build/builds/mbus-httpd/target/debug/mbus ./mbus-httpd
RUN mkdir static
RUN wget --quiet https://raw.githubusercontent.com/packom/mbus-api/master/api/openapi.yaml -O static/api.yaml
ENV LIBMBUS_PATH=/
VOLUME ["/ssl"]
EXPOSE 8080
CMD ["/mbus-httpd"]
