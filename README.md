# machine_avr

Classic **megaAVR** port of a MicroPython-shaped **`machine`** API for
[Klin](https://github.com/klin-lang/klin).

Not a MicroPython port. No GC, no hidden heap — Pin / buses configure
classic `PORTx` / Timer / USART / TWI / SPI / ADC MMIO explicitly.

For **ATxmega** (different PORT map @ `0x0600`), see
[`machine_xmega`](https://github.com/klin-lang/machine_xmega).
For modern **tinyAVR** (AVRxt + UPDI), see Klin issue
[141](https://github.com/klin-lang/klin/blob/main/issues/141-machine-tinyavr.md)
(`machine_tinyavr` — not this package).

Decision / catalog: [Klin issue 061](https://github.com/klin-lang/klin/blob/main/issues/061-micropython-machine-api.md).
Leonardo plan: [Klin issue 142](https://github.com/klin-lang/klin/blob/main/issues/142-machine-avr-atmega32u4.md).

## Status

| API | Status |
|---|---|
| `Pin` ATmega328P (`pin_out` / `pin_in`) | ✅ |
| `Pin` ATmega2560 (`pin_out_2560` / `pin_in_2560`) | ✅ |
| `Pin` ATmega32U4 (`pin_out_32u4` / `pin_in_32u4`) | ✅ |
| `Pwm` 328P (`pwm_out`) | ✅ Timer1/Timer2 |
| `Pwm` 32U4 (`pwm_out_32u4`) | ✅ Timer1/Timer3 |
| `Rc` (`rc_out` / `rc_out_32u4`) | ✅ |
| `Uart` 328P USART0 (`uart_out`) | ✅ |
| `Uart` 32U4 USART1 (`uart_out_32u4`) | ✅ (not USB CDC) |
| `I2c` (`i2c_out` / `i2c_out_32u4`) | ✅ TWI master |
| `Spi` (`spi_out` / `spi_out_32u4`) | ✅ soft NSS |
| `Adc` (`adc_out` / `adc_out_32u4`) | ✅ 10-bit |
| `Dac` | ❌ not on 328P / 2560 / 32U4 |
| `USBDevice` (32U4 CDC/HID) | ⏳ later |

`version()` is `3` (`@v0.3.0`).

No runtime chip detect. Arduino-style `D13` mapping is board-level — this
package uses **port letter + bit** (Uno LED = `Port.B`, 5; Mega LED =
`Port2560.B`, 7; Leonardo LED = `Port32U4.C`, 7). Clocks are explicit
(`*_clk_hz`).

## Requirements

- [Klin](https://github.com/klin-lang/klin) compiler (`klin` or `dart run path/to/bin/klin.dart`)
- For board images: `avr-gcc` + device CRT / linker (`atmega328p` / `atmega2560` / `atmega32u4`)
- Flash Leonardo with `avrdude` (Caterina); DFU optional

## Layout

```text
machine_avr/             # module machine_avr (directory package)
  version.kl             # version() → 3
  pin.kl / pwm.kl / rc.kl / uart.kl / i2c.kl / spi.kl / adc.kl
  *_test.kl              # skipped on import
examples/blink_uno/ … adc_uno/
examples/blink_leonardo/ … adc_leonardo/
```

## Usage — ATmega328P (Uno / Nano / Pro Mini)

```klin
import "github/klin-lang/machine_avr" machine

fn main() {
    let led = machine.pin_out(machine.Port.B, 5)  // D13
    let pwm = machine.pwm_out(machine.Port.B, 1, 1, 1, 16000000) // D9 OC1A
    pwm.freq(1000)
    pwm.duty_u16(32768)

    let u = machine.uart_out(machine.Port.D, 1, machine.Port.D, 0, 16000000, 9600)
    let i2c = machine.i2c_out(machine.Port.C, 4, machine.Port.C, 5, 16000000, 100000)
    let adc = machine.adc_out(machine.Port.C, 0, 0) // A0
    let _ = adc.read_u16()
    led.toggle()
    u.write_u8(65)
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

## Usage — ATmega32U4 (Leonardo / Micro / Pro Micro)

```klin
import "github/klin-lang/machine_avr" machine

fn main() {
    let led = machine.pin_out_32u4(machine.Port32U4.C, 7)  // D13
    let pwm = machine.pwm_out_32u4(machine.Port32U4.B, 5, 1, 1, 16000000) // D9
    let u = machine.uart_out_32u4(
        machine.Port32U4.D, 3, machine.Port32U4.D, 2, 16000000, 9600) // Serial1
    let adc = machine.adc_out_32u4(machine.Port32U4.F, 7, 7) // A0
    led.toggle()
}
```

```sh
klin get github/klin-lang/machine_avr@v0.3.0
```

## Examples

```sh
cd examples/blink_uno   # also: pwm_uno, rc_uno, uart_uno, i2c_uno, spi_uno, adc_uno
make emit KLIN=/path/to/klin/bin/klin.dart
# → out/*.c

cd examples/blink_leonardo  # also: pwm_leonardo, uart_leonardo, adc_leonardo
make emit KLIN=/path/to/klin/bin/klin.dart
```

Linking a flashable ELF needs your board’s startup and linker script
(avr-gcc device pack). This package owns Pin / bus MMIO; CI validates
host tests + `--emit-c`.

## Tests

```sh
dart run /path/to/klin/bin/klin.dart test machine_avr/
```

## License

MIT
