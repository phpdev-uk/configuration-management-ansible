all: pdf

output/:
	mkdir output

pdf: output/notes-print.pdf output/notes-screen.pdf output/requirements-print.pdf output/requirements-screen.pdf

output/%-print.pdf: %.md output/
	filepp $< | pandoc -o $@

output/%-screen.pdf: %.md output/
	filepp $< | pandoc -V mainfont=Ubuntu --latex-engine=xelatex -o $@

clean:
	rm -rf output/

.PHONY: clean
