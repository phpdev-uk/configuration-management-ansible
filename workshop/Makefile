all: notes-print.pdf notes-screen.pdf requirements-print.pdf requirements-screen.pdf

%-print.pdf: %.md
	filepp $< | pandoc -o $@

%-screen.pdf: %.md
	filepp $< | pandoc -V mainfont=Ubuntu --latex-engine=xelatex -o $@

clean:
	rm -rf *.pdf
	rm -rf *.log

.PHONY: clean
