OUTPUT_FILES = output/notes.pdf output/requirements.pdf output/notes.html output/requirements.html
COMMON = ../common/*.md
VAGRANT_FILES = exercises/*/Vagrantfile
CSS_FILE=workshop.css

all: $(OUTPUT_FILES)

output/%.pdf: %.md $(COMMON) $(VAGRANT_FILES)
	filepp $< | pandoc -o $@

output/%.html: %.md $(COMMON) $(VAGRANT_FILES) $(CSS_FILE)
	filepp $< | pandoc -s --include-in-header $(CSS_FILE) -o $@

clean:
	rm -f output/*

.PHONY: clean
