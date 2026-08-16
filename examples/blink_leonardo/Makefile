# ATmega32U4 blink (Leonardo / Micro / Pro Micro): Klin --emit-c.
#
#   make emit KLIN=/path/to/klin/bin/klin.dart
#   → out/blink.c
#
# Flash with avr-gcc CRT for atmega32u4 + avrdude (Caterina).
# LED D13 = PC7 (not PB5 like Uno).

KLIN ?= klin

.PHONY: all emit clean

all: emit

emit: out/blink.c

out/blink.c out/blink.link: blink.kl $(wildcard ../../machine_avr/*.kl)
	@mkdir -p out
	@case "$(KLIN)" in \
	  *.dart) dart run "$(KLIN)" --emit-c blink.kl ;; \
	  *) "$(KLIN)" --emit-c blink.kl ;; \
	esac

clean:
	rm -rf out
