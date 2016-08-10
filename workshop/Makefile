SRC=notes.md
PDF=$(SRC:.md=.pdf)

all: $(PDF)

%.pdf: %.md
	pandoc $< -o $@

clean:
	rm -rf *.pdf

.PHONY: clean
