KLIN ?= klin

.PHONY: all emit clean

all: emit

emit: out/spi.c

out/spi.c out/spi.link: spi.kl $(wildcard ../../machine_avr/*.kl)
	@mkdir -p out
	@case "$(KLIN)" in \
	  *.dart) dart run "$(KLIN)" --emit-c spi.kl ;; \
	  *) "$(KLIN)" --emit-c spi.kl ;; \
	esac

clean:
	rm -rf out
