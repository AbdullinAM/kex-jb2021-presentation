
.PHONY: all

%.eps: %.svg
	inkscape -S $*.svg --export-type=eps --export-filename=$*.eps


all: kexOverview.eps jetbrainsResearch.eps polytech.eps memoryModel.eps oldMemoryModel.eps oldRT.eps oldRt2.eps

clean:
	rm *.eps
