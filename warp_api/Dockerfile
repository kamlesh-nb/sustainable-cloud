#stage 1
FROM rust:latest as planner
WORKDIR /app

RUN cargo install cargo-chef
COPY . .

RUN cargo chef prepare --recipe-path recipe.json

#stage 2
FROM rust:latest as cacher
WORKDIR /app
RUN cargo install cargo-chef
COPY --from=planner /app/recipe.json recipe.json
RUN cargo chef cook --release --recipe-path recipe.json

#stage 3
FROM rust:latest as builder
COPY . /app
WORKDIR /app
COPY --from=cacher /app/target target
COPY --from=cacher /usr/local/cargo /usr/local/cargo

RUN cargo build --release 
FROM gcr.io/distroless/cc-debian12:latest
WORKDIR /app

COPY --from=builder /app/target/release/warp_api .
EXPOSE 5041
CMD ["./warp_api"]
