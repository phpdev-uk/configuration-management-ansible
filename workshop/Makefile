PDF_FILES = output/notes.pdf output/requirements.pdf
COMMON = ../common/*.md

all: $(PDF_FILES)

output/%.pdf: %.md $(COMMON)
	filepp $< | pandoc -o $@

clean:
	rm -f output/*

.PHONY: clean
