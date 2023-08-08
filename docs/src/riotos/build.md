# Built & Run Instructions


For the Rust components we first need [`rustup`](https://rustup.rs/):

- `curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh`


For building the `c2rust` bindings we need a few more dependencies, mainly a `LLVM` toolchain:

```
# ubuntu:22.04

apt-get install cmake pkg-config

apt-get install \
        llvm-14 \
        clang-14 \
        clang-tools-14 \
        lld-14 \
        llvm \
        clang \
        clang-tools

apt-get install \
        libclang-dev \
        libssl-dev \
        llvm-dev \
        libclang-14-dev
        
cargo install c2rust --git https://github.com/immunant/c2rust
```

Building `RIOT` itself only requires a few build tools

- `apt-get install gcc-arm-none-eabi make gcc-multilib libstdc++-arm-none-eabi-newlib openocd gdb-multiarch doxygen wget unzip python3-serial` (ubuntu:22.04)

We then configure `rustup` to use the right toolchain:

- `rustup target add thumbv7em-none-eabihf --toolchain stable`

clone `RIOT`:

- `git clone https://github.com/RIOT-OS/RIOT`

copy our examples into the `examples` folder and build them:

- `cd RIOT && make BOARD=nrf52840dk -C examples/rust-nimble_advertiser`
- `cd RIOT && make BOARD=nrf52840dk -C examples/rust-nimble_scanner`

The build artifacts we care about in the end are:

- `RIOT/examples/rust-nimble_advertiser/bin/nrf52840dk/nimble_advertiser.elf` - The build advertiser
- `RIOT/examples/rust-nimble_advertiser/bin/nrf52840dk/nimble_scanner.elf` - The build scanner


## Build with Docker

A `Dockerfile` is additionally provided that when ran will build all these artifacts [here](TODO: Link Here).

By running `docker build --output=output --target=binaries .` The docker image will be build and the output artifacts will be extracted and put into a folder called `output`.

## Run on Device

The [recommended way](https://doc.riot-os.org/flashing.html) of flashing and interacting with the device is using `RIOT`'s build system:

`PROGRAMMER=jlink make BOARD=nrf52840dk -C examples/<example> flash `
`PROGRAMMER=jlink make BOARD=nrf52840dk -C examples/<example> term`

This will directly rebuild and flash the examples to the device.

## Run on Renode
The build ELF files can directly be loaded into `renode`.

The examples provide two `renode` scenarios to play around with: `riotos_advertise` and `riotos_demo`.

### `riotos_advertise`

Run with:

- `renode -e "include @riotos_advertise.resc;start"`

This starts the advertising example and Wireshark to capture the Bluetooth Low Energy Traffic:

![](advertise.png)
![](packet.png)

### `riotos_demo`

Run with:

- `renode -e "include @riotos_demo.resc;start"`

This starts both the advertising and scanning examples.


### Notes on renode:

`RIOT`s wiki has a [small section](https://doc.riot-os.org/emulators.html) on how it already supports `renode` as an emulator, but when trying to emulate device specific functionality like BLE we still have a problem for the NRF boards.
Specifically the way `RIOT` implemented UART for the `nrf52840` by relying on the `Shortcut` functionality that is not implemented (as of yet) in `renode`'s `nrf52840` UART driver.
This means we need to load a patched [`NRF52840_UART_MODIFIED.cs`](TODO: Link here) driver and use it instead. 