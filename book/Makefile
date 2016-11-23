PDF_FILES = output/book-screen.pdf output/book-print.pdf
PANDOC_FLAGS = --toc --chapters
PANDOC_PRINT_FLAGS = $(PANDOC_FLAGS)
PANDOC_SCREEN_FLAGS = -V mainfont=Ubuntu --latex-engine=xelatex $(PANDOC_FLAGS)

all: $(PDF_FILES)

output/book-screen.pdf: chapters/*.md ../common/*.md
	sed -s '$$a\\n' chapters/*.md | filepp -c | pandoc $(PANDOC_SCREEN_FLAGS) -o $@

output/book-print.pdf: chapters/*.md ../common/*.md
	sed -s '$$a\\n' chapters/*.md | filepp -c | pandoc $(PANDOC_PRINT_FLAGS) -o $@

clean:
	rm -f output/*

.PHONY: clean
