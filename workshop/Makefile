PDF_FILES = output/notes.pdf output/requirements.pdf
COMMON = ../common/*.md
VAGRANT_FILES = exercises/*/Vagrantfile

all: $(PDF_FILES)

output/%.pdf: %.md $(COMMON) $(VAGRANT_FILES)
	filepp $< | pandoc -o $@

clean:
	rm -f output/*

.PHONY: clean
