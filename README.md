# machine_avr

Classic **megaAVR** port of a MicroPython-shaped **`machine`** API for
[Klin](https://github.com/klin-lang/klin).

Not a MicroPython port. No GC, no hidden heap — Pin / buses configure
classic `PORTx` / Timer / USART / TWI / SPI / ADC MMIO explicitly.

For **ATxmega** (different PORT map @ `0x0600`), see
[`machine_xmega`](https://github.com/klin-lang/machine_xmega).

Decision / catalog: [Klin issue 061](https://github.com/klin-lang/klin/blob/main/issues/061-micropython-machine-api.md).

## Status

| API | Status |
|---|---|
| `Pin` ATmega328P (`pin_out` / `pin_in`) | ✅ MVP |
| `Pin` ATmega2560 (`pin_out_2560` / `pin_in_2560`) | ✅ MVP |
| `Pwm` | ✅ Timer1/Timer2 (`pwm_out`) |
| `Rc` | ✅ servo helper on `Pwm` (`rc_out`) |
| `Uart` | ✅ USART0 (`uart_out`) |
| `I2c` | ✅ TWI master (`i2c_out`) |
| `Spi` | ✅ SPI master soft NSS (`spi_out`) |
| `Adc` | ✅ 10-bit ADC (`adc_out`) |
| `Dac` | ❌ not available on ATmega328P / ATmega2560 |
| `Signal` / `I2S` / `RTC` / `Timer` / `WDT` / `SDCard` / `USBDevice` | ⏳ later |
| tinyAVR / AVR Dx | later (different IO) |

`version()` is `2` (`@v0.2.0`).

No runtime chip detect. Arduino-style `D13` mapping is board-level — this
package uses **port letter + bit** (Uno LED = `Port.B`, 5; Mega LED =
`Port2560.B`, 7). Clocks are explicit (`*_clk_hz`).

## Requirements

- [Klin](https://github.com/klin-lang/klin) compiler (`klin` or `dart run path/to/bin/klin.dart`)
- For board images: `avr-gcc` + device CRT / linker (`atmega328p` / `atmega2560`)

## Layout

```text
machine_avr/             # module machine_avr (directory package)
  version.kl             # version() → 2
  pin.kl / pwm.kl / rc.kl / uart.kl / i2c.kl / spi.kl / adc.kl
  *_test.kl              # skipped on import
examples/blink_uno/ … adc_uno/
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

```sh
klin get github/klin-lang/machine_avr@v0.2.0
```

## Examples

```sh
cd examples/blink_uno   # also: pwm_uno, rc_uno, uart_uno, i2c_uno, spi_uno, adc_uno
make emit KLIN=/path/to/klin/bin/klin.dart
# → out/*.c
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
