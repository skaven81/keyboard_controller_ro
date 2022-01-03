Arduino Keyboard Controller
===========================

Arduino Uno based PS/2 keyboard controller for my homebrew CPU.

Pin assignments
---------------

* PS/2 Data: D1
* PS/2 Clock: D2
* Interrupt: D3 (pulses low to signal an interrupt)
* Shift register clock: D4 (assumes a '595 shift register with clocks tied together)
* Shift register data: D5

Usage
-----

### Startup

The controller takes quite some time to initialize.  Once the keyboard is
running, the controller will flash the keyboard LEDs to indicate that it is ready.

### Keypress sequence

When a keyboard "make" event is detected, for a "normal" keypress (not a special
key like Shift or Ctrl alone) the translated key character is shifted out,
least significant bit first.  Then the flags for the keypress are shifted out,
least significant bit first.  The result is 16 bits of data shifted out.

After shifting out the key character and flags, the interrupt pin is pulsed low.
An /S/R latch is expected to be used to hold the interupt pin low until the
CPU has retrieved the data from the shift registers.

### Key flags

The flags shift register may have the following bits set:

* `KEYFLAG_MAKEBREAK   0x01` - make=1, break=0
* `KEYFLAG_SHIFT       0x02`
* `KEYFLAG_CTRL        0x04`
* `KEYFLAG_ALT         0x08`
* `KEYFLAG_SUPER       0x10`
* `KEYFLAG_GUI         0x20`
* `KEYFLAG_FUNCTION    0x40` - One of the F-keys was pressed

A set bit indicates that the key is currently pressed.

* Function keys F1-F9 are indicated by an ASCII 1-9 with `KEYFLAG_FUNCTION` set.
* Function key F10 is indicated by an ASCII 0 with `KEYFLAG_FUNCTION` set.
* Function keys F11-12 are indicated by ASCII `!` and `@` with `KEYFLAG_FUNCTION` set.

