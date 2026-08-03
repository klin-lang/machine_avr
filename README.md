# machine_avr

Classic **megaAVR** port of a MicroPython-shaped **`machine`** API for
[Klin](https://github.com/klin-lang/klin).

Not a MicroPython port. No GC, no hidden heap — Pin configures `DDRx` /
`PORTx` / `PINx` explicitly via MMIO.

For **ATxmega** (different PORT map @ `0x0600`), see
[`machine_xmega`](https://github.com/klin-lang/machine_xmega).

Decision / catalog: [Klin issue 061](https://github.com/klin-lang/klin/blob/main/issues/061-micropython-machine-api.md).

## Status

| API | Status |
|---|---|
| `Pin` ATmega328P (`pin_out` / `pin_in`) | MVP (`@v0.1.0`) |
| `Pin` ATmega2560 (`pin_out_2560` / `pin_in_2560`) | MVP (`@v0.1.0`) |
| `Pwm`, `Uart`, … | later |
| tinyAVR / AVR Dx | later (different IO) |

No runtime chip detect. Arduino-style `D13` mapping is board-level — this
package uses **port letter + bit** (Uno LED = `Port.B`, 5; Mega LED =
`Port2560.B`, 7).

## Requirements

- [Klin](https://github.com/klin-lang/klin) compiler (`klin` or `dart run path/to/bin/klin.dart`)
- For board images: `avr-gcc` + device CRT / linker (`atmega328p` / `atmega2560`)

## Layout

```text
machine_avr/             # module machine_avr (directory package)
  version.kl             # version() → 1
  pin.kl
  pin_test.kl            # skipped on import
examples/blink_uno/      # PB5 toggle (Arduino Uno D13)
examples/blink_mega/     # PB7 toggle (Arduino Mega D13)
```

## Usage — ATmega328P (Uno / Nano / Pro Mini)

```klin
import "github/klin-lang/machine_avr" machine

fn main() {
    let led = machine.pin_out(machine.Port.B, 5)  // D13
    while true {
        led.toggle()
    }
}
```

## Usage — ATmega2560 (Arduino Mega)

```klin
import "github/klin-lang/machine_avr" machine

fn main() {
    let led = machine.pin_out_2560(machine.Port2560.B, 7)  // D13
    while true {
        led.toggle()
    }
}
```

```sh
klin get github/klin-lang/machine_avr@v0.1.0
```

## Examples

```sh
cd examples/blink_uno
make emit KLIN=/path/to/klin/bin/klin.dart
# → out/blink.c
```

Linking a flashable ELF needs your board’s startup and linker script
(avr-gcc device pack). This package only owns the Pin MMIO.

## Tests

```sh
dart run /path/to/klin/bin/klin.dart test machine_avr/
```

## License

MIT
