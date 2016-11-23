PDF_FILES = output/book-screen.pdf output/book-print.pdf
CHAPTERS = chapters/*.md
PANDOC_FLAGS = --toc --chapters
PANDOC_PRINT_FLAGS = $(PANDOC_FLAGS)
PANDOC_SCREEN_FLAGS = -V mainfont=Ubuntu --latex-engine=xelatex $(PANDOC_FLAGS)

all: $(PDF_FILES)

output/book-screen.pdf: $(CHAPTERS) ../common/*.md
	sed -s '$$a\\n' $(CHAPTERS) | filepp -c | pandoc $(PANDOC_SCREEN_FLAGS) -o $@

output/book-print.pdf: $(CHAPTERS) ../common/*.md
	sed -s '$$a\\n' $(CHAPTERS) | filepp -c | pandoc $(PANDOC_PRINT_FLAGS) -o $@

clean:
	rm -f output/*

.PHONY: clean
