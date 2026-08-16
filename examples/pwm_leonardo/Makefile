# ATmega32U4 Timer1 PWM (Leonardo D9).

KLIN ?= klin

.PHONY: all emit clean

all: emit

emit: out/pwm.c

out/pwm.c out/pwm.link: pwm.kl $(wildcard ../../machine_avr/*.kl)
	@mkdir -p out
	@case "$(KLIN)" in \
	  *.dart) dart run "$(KLIN)" --emit-c pwm.kl ;; \
	  *) "$(KLIN)" --emit-c pwm.kl ;; \
	esac

clean:
	rm -rf out
