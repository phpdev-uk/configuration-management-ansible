PDF_FILES = output/book-screen.pdf output/book-print.pdf

all: $(PDF_FILES)

output/:
	mkdir output

$(PDF_FILES): output/ chapters/*.md ../common/*.md

output/book-screen.pdf:
	sed -s '$$a\\n' chapters/*.md | filepp -c | pandoc -V mainfont=Ubuntu --latex-engine=xelatex --toc --chapters -o $@

output/book-print.pdf:
	sed -s '$$a\\n' chapters/*.md | filepp -c | pandoc --toc --chapters -o $@

clean:
	rm -rf output/

.PHONY: clean
