# ATmega2560 blink: Klin --emit-c (link with avr-gcc CRT for your board).
#
#   make emit KLIN=/path/to/klin/bin/klin.dart
#   → out/blink.c

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
