KLIN ?= klin

.PHONY: all emit clean

all: emit

emit: out/adc.c

out/adc.c out/adc.link: adc.kl $(wildcard ../../machine_avr/*.kl)
	@mkdir -p out
	@case "$(KLIN)" in \
	  *.dart) dart run "$(KLIN)" --emit-c adc.kl ;; \
	  *) "$(KLIN)" --emit-c adc.kl ;; \
	esac

clean:
	rm -rf out
