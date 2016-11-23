all: output/book-screen.pdf

output/book-screen.pdf: chapters/*.md ../common/*.md
	cat chapters/*.md | filepp -c | pandoc -V mainfont=Ubuntu --latex-engine=xelatex --toc --chapters -o $@

clean:
	rm -f output/*

.PHONY: clean
