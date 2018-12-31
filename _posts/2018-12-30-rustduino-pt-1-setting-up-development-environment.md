---
title: "Rustduino pt. I: setting up the development environment"
description: Installing and configuring a development environment to cross-compile rust programs for the AVR architecture and the Arduino UNO board.
---

This post details the steps needed to start programming an `Arduino UNO` board using the `rust` programming language and working on Mac OS.

## Premise

As a software developer who has always focused on high-level languages and frameworks, I've been meaning to explore the world of system programming for quite a while. Beyond satisfying my general fascination with technology, I am convinced that spending some time closer to the hardware level will make me a better programmer overall and give me a chance to learn some valuable skills.

Fast forward to the ongoing winter festivities and I find myself, by coincidence, with everything I need to begin my discovery:

1) some lovely spare time;
2) an `Arduino UNO` board based on the `ATMega328P` microcontroller;
3) preliminary support for the microcontroller's `AVR` architecture in `rust`, an intriguing and relatively new programming language that I was already on my list of things to play with in 2019.

I've decided to document my experiments in a series of blog posts, hopefully sparing a couple of headaches to other developers getting into these subjects. This first post deals with setting up the development environment. Although I work on a Mac and some of these instructions are specific to Mac OS, porting them to Linux should be relatively trivial.

## Install XCode's developer tools and Mac OS' development headers

Start by installing `XCode`'s command-line developer tools:

```bash
xcode-select --install
```

If on Mac OS 10.14 (Mojave) install the OS' development headers:

```bash
sudo installer -pkg /Library/Developer/CommandLineTools/Packages/macOS_SDK_headers_for_macOS_10.14.pkg -target /
```

Missing headers will result in issues while building the `zlib` library and/or errors while unpacking `tar` archives using `python`'s `zlib` module.

## Install Python 2.7.x

Install `python` via `homebrew`:

```bash
brew update
brew install cmake openssl readline sqlite3 xz zlib python@2
```

## Install the GCC AVR toolchain

Install the GCC AVR toolchain, once again via `homebrew`:

```bash
brew update
brew tap osx-cross/avr
brew install avr-gcc # This takes a while!
```

This step requires compiling GCC, which takes ~30 minutes. Many thanks to all the partecipants in the discussion related to [this issue on GitHub][2] as it helped me figure out what I needed to install.

## Install the standard `rust` toolchain

Install the standard `rust` toolchain by following [the official guide][6]. This will also install the `rustup` toolchain management tool, which helps managing multiple `rust` builds on the same machine.

## Install the AVR `rust` toolchain

Time to install the `rust` toolchain that targets the `AVR` architecture, which is the architecture of the `ATMega328P` that powers the `Arduino UNO`. Follow the steps in the [repository's README][4], which I have included below as I've had to slightly adapt them to work on Mac OS.

```bash
# Grab the avr-rust sources
git clone https://github.com/avr-rust/rust.git avr-rust

# Create a directory to place built files in
mkdir avr-rust-build
cd avr-rust-build

# Generate Makefile using settings suitable for an experimental compiler
../avr-rust/configure \
  --enable-debug \
  --disable-docs \
  --enable-llvm-assertions \
  --enable-debug-assertions \
  --enable-optimize \
  --enable-llvm-release-debuginfo \
  --experimental-targets=AVR \
  --prefix=/opt/avr-rust

# Build the compiler
make  # This takes a while!

# Register the new toolchain with rustup
rustup toolchain link avr-toolchain <absolute path to avr-rust-build>/build/x86_64-apple-darwin/stage1
```

## Compile the `blink` example project

Now that the AVR toolchain is ready, it's time to compile a small test program to later deploy on the Arduino UNO. Follow the instructions in the [official repo's README][10], included below as I've had to tweak them a little:

```bash
git clone https://github.com/avr-rust/blink.git avr-rust-blink
cd avr-rust-blink
export RUST_TARGET_PATH=`pwd`
export XARGO_RUST_SRC=<absolute path to avr-rust>/src	# not avr-rust-build!
rustup run avr-toolchain xargo build --target avr-atmega328p --release
```

This should have produced an `.elf` file at `target/avr-atmega328p/release/blink.elf`.

## Install the Arduino IDE and `avrdude`

Download it from [Arduino's home page][3] and install it as any other Mac OS application by moving it to the `/Applications` folder. Use it from the command line as follows:

```bash
/Applications/Arduino.app/Contents/MacOS/Arduino
```

Although we will not use it in the rest of this post, the IDE will become necessary in the future. For the time being,  `avrdude` will take care of updating compiled programs to the board. Install it from homebrew:

```bash
brew update
brew install avrdude
```

## Deploy onto the Arduino UNO

Connect the Arduino UNO via USB to the Mac. It should come up as a `/dev/tty.usbmodem****`. Use `avrdude` to upload the compiled program to the board:

```bash
avrdude -v -p atmega328p -c arduino -P /dev/tty.usbmodem14201 -b 115200 -D -Uflash:w:"target/avr-atmega328p/release/blink.elf"
```

Further useful pointers [here][7],  [here][8] and [here][9].

## Conclusion

Voilà! We now have a development environment through which we can cross-compile our programs for the `AVR` architecture.

[1]: https://github.com/osx-cross/homebrew-avr
[2]: https://github.com/avr-rust/blink/issues/6
[3]: https://www.arduino.cc
[4]: https://github.com/avr-rust/rust
[5]: https://www.rust-lang.org
[6]: https://www.rust-lang.org/tools/install
[7]: https://arduino.stackexchange.com/questions/15893/how-to-compile-upload-and-monitor-via-the-linux-command-line
[8]: https://forum.arduino.cc/index.php?topic=313868.0
[9]: https://arduino.stackexchange.com/questions/17938/how-to-flash-the-atmega328-with-avrdude
[10]: https://github.com/avr-rust/blink
