# name of your application
APPLICATION = nimble_advertiser

# If no BOARD is found in the environment, use this default:
BOARD ?= native

# This has to be the absolute path to the RIOT base directory:
RIOTBASE ?= $(CURDIR)/../..

# Comment this out to disable code in RIOT that does safety checking
# which is not needed in a production environment but helps in the
# development process:
DEVELHELP ?= 1

# Change this to 0 show compiler invocation lines by default:
QUIET ?= 1

# The name of crate (as per Cargo.toml package name, but with '-' replaced with '_')
APPLICATION_RUST_MODULE = rust_nimble_advertiser
BASELIBS += $(APPLICATION_RUST_MODULE).module


USEMODULE += shell

# configure and use Nimble
USEPKG += nimble

FEATURES_REQUIRED += rust_target

# All Rust components RIOT uses work on stable Rust. If any extra libraries
# were to require a more recent version, switch to `CARGO_CHANNEL =
# $(CARGO_CHANNEL_NIGHTLY)` to use whichever nightly version is available.
CARGO_CHANNEL ?= stable

# Currently unknown, something related to the LED_PORT definition that doesn't
# pass C2Rust's transpilation
BOARD_BLACKLIST := ek-lm4f120xl

include $(RIOTBASE)/Makefile.include
