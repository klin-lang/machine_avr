# ATmega32U4 USART1 example (Leonardo Serial1).

KLIN ?= klin

.PHONY: all emit clean

all: emit

emit: out/uart.c

out/uart.c out/uart.link: uart.kl $(wildcard ../../machine_avr/*.kl)
	@mkdir -p out
	@case "$(KLIN)" in \
	  *.dart) dart run "$(KLIN)" --emit-c uart.kl ;; \
	  *) "$(KLIN)" --emit-c uart.kl ;; \
	esac

clean:
	rm -rf out
