
.PHONY: all

%.eps: %.svg
	inkscape -S $*.svg --export-type=eps --export-filename=$*.eps


all: jetbrainsResearch.eps polytech.eps memoryModel.eps oldMemoryModel.eps

clean:
	rm *.eps
