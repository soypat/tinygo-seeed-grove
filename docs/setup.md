# Setup

**toolchain**: the set of programs used to turn source code into a running program.

Before the fun starts we need three things on your computer: the Go compiler,
the TinyGo compiler, and this repository. Then we'll flash a program to the board
to prove the whole pipeline works. Settle in, this takes about fifteen minutes.

## Why two compilers?

Go's standard compiler produces programs for computers with operating systems,
megabytes of memory and file systems. Our XIAO ESP32-C3 has 400 **kilo**bytes of
RAM and no operating system at all — the program you flash *is* the only thing
running. [TinyGo](https://tinygo.org) is a different compiler for the same Go
language, built for exactly this world. You write ordinary Go; TinyGo makes it
small enough to live on a microcontroller. It still needs the regular Go
toolchain installed to do its job, which is why we install both.

## 1. Install Go

Follow the official instructions at [go.dev/doc/install](https://go.dev/doc/install).
Verify:

```sh
go version
```

## 2. Install TinyGo

Follow [tinygo.org/getting-started/install](https://tinygo.org/getting-started/install/)
for your operating system. Verify:

```sh
tinygo version
```

## 3. Get this repository

```sh
git clone https://github.com/soypat/seeed-grove
cd seeed-grove
```

Everything in this guide is run from the root of this repository.

## 4. Plug in the board

Take the XIAO ESP32-C3 (leave the shield aside for a moment) and connect it to
your computer with the USB-C cable. No drivers needed on most systems — the
ESP32-C3 talks USB directly.

**Linux users**: your user needs permission to open serial ports. If flashing
fails with a permissions error:

```sh
sudo usermod -aG dialout $USER   # then log out and back in
```

## 5. Flash your first program

Here is the whole program — an embedded systems tradition:

<!-- code: examples/hello-world/hello-world.go -->
```go
package main

import (
	"time"
)

func main() {
	for {
		time.Sleep(time.Second)
		println("hello-world")
	}
}
```

[view source](../examples/hello-world/hello-world.go)

No `machine` imports, no hardware, just an infinite loop printing a greeting.
Where does `println` print to when there is no screen and no terminal? Over the
USB cable, back to your computer. Flash it:

```sh
tinygo flash -target=xiao-esp32c3 ./examples/hello-world/
```

The `-target` flag tells TinyGo everything about the board: which chip, which
pins, how to upload. That one flag is the only board-specific thing you will
ever type. Now listen to the board:

```sh
tinygo monitor
```

You should see `hello-world` arrive once per second. **Congratulations** — you
have compiled Go on your machine and executed it on a machine the size of a
stamp. Press `Ctrl-C` to stop monitoring (the board keeps running regardless;
it needs your computer for nothing but power now).

> **If flashing fails**: unplug the board, hold down the small `B` (BOOT) button
> while plugging it back in, release, and flash again. This forces the chip into
> its bootloader. Also double-check the cable — some USB-C cables are
> power-only and carry no data.

## 6. Install the XIAO onto the shield

Now push the XIAO into the Grove shield's two pin sockets (the USB port
lines up with the shield's edge). The shield has 8 identical white sockets
called **Grove connectors**. In code we address them through the
[`grove`](../grove.go) package as `shield.Conn(0)` through `shield.Conn(7)`,
with the shield held horizontally and with the USB port on the left,
they're indexed left to right, top to bottom:

| Conn index | Capability |
|------------|-----------|
| 0, 1, 2 | Analog input (ADC) & digital I/O |
| 3, 4 | I2C bus (both sockets share the same bus) |
| 5 | UART |
| 6, 7 | Digital I/O |

Every Grove cable carries 4 wires: **yellow** (signal), **white** (secondary
signal), **red** (3.3V power) and **black** (ground). One socket, one cable,
impossible to plug in backwards. This is the entire wiring story for the whole
kit.

## Takeaways

Your toolchain is: edit Go → `tinygo flash -target=xiao-esp32c3 ./path/` →
`tinygo monitor`. That loop is all there is; every chapter from here on just
changes what's inside the Go file. Time to make some noise:
**[Chapter 1 — the buzzer](devices/01-buzzer.md)**.
