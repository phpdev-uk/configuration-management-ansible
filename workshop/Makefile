PDF_FILES = output/notes-print.pdf output/notes-screen.pdf output/requirements-print.pdf output/requirements-screen.pdf

all: $(PDF_FILES)

output/%-print.pdf: %.md
	filepp $< | pandoc -o $@

output/%-screen.pdf: %.md
	filepp $< | pandoc -V mainfont=Ubuntu --latex-engine=xelatex -o $@

clean:
	rm -f output/*

.PHONY: clean
