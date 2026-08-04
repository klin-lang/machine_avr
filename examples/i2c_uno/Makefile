KLIN ?= klin

.PHONY: all emit clean

all: emit

emit: out/i2c.c

out/i2c.c out/i2c.link: i2c.kl $(wildcard ../../machine_avr/*.kl)
	@mkdir -p out
	@case "$(KLIN)" in \
	  *.dart) dart run "$(KLIN)" --emit-c i2c.kl ;; \
	  *) "$(KLIN)" --emit-c i2c.kl ;; \
	esac

clean:
	rm -rf out
