MAIN := Lumina_Retail_Informe
LATEXMK := latexmk
FLAGS := -pdf -interaction=nonstopmode -halt-on-error

.PHONY: all pdf clean rebuild open

all: pdf

pdf:
	$(LATEXMK) $(FLAGS) $(MAIN).tex

clean:
	$(LATEXMK) -C $(MAIN).tex

rebuild: clean pdf

open: pdf
	explorer.exe "$$(wslpath -w $(MAIN).pdf)"