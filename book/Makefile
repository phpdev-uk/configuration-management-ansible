all: output/book-screen.pdf

output/book-screen.pdf: chapters/*.md
	pandoc -V mainfont=Ubuntu --latex-engine=xelatex --toc --chapters chapters/*.md -o $@

clean:
	rm -f output/*

.PHONY: clean
