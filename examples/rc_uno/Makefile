KLIN ?= klin

.PHONY: all emit clean

all: emit

emit: out/rc.c

out/rc.c out/rc.link: rc.kl $(wildcard ../../machine_avr/*.kl)
	@mkdir -p out
	@case "$(KLIN)" in \
	  *.dart) dart run "$(KLIN)" --emit-c rc.kl ;; \
	  *) "$(KLIN)" --emit-c rc.kl ;; \
	esac

clean:
	rm -rf out
