SRC=notes.md
PDF=$(SRC:.md=.pdf)

all: notes-print.pdf notes-screen.pdf

notes-print.pdf: notes.md
	pandoc $< -o $@

notes-screen.pdf: notes.md
	pandoc -V mainfont=Ubuntu --latex-engine=xelatex $< -o $@

clean:
	rm -rf *.pdf

.PHONY: clean
