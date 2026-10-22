MAIN = Lumina_Retail_Informe

.PHONY: all clean

all:
	latexmk -pdf -interaction=nonstopmode -halt-on-error $(MAIN).tex

clean:
	latexmk -c $(MAIN).tex
