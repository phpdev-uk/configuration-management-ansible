PDF_FILES = output/book-screen.pdf output/book-print.pdf
PANDOC_FLAGS = --toc --chapters

all: $(PDF_FILES)

output/book-screen.pdf: chapters/*.md ../common/*.md
	sed -s '$$a\\n' chapters/*.md | filepp -c | pandoc -V mainfont=Ubuntu --latex-engine=xelatex $(PANDOC_FLAGS) -o $@

output/book-print.pdf: chapters/*.md ../common/*.md
	sed -s '$$a\\n' chapters/*.md | filepp -c | pandoc $(PANDOC_FLAGS) -o $@

clean:
	rm -f output/*

.PHONY: clean
