VERSION=0.2.2
ORIG_TARGZ=quotez_$(VERSION).orig.tar.gz

all: quotez

quotez:
	git clone https://github.com/pseudocc/quotez.git $@
	cd $@ && git checkout tags/v$(VERSION) && git switch -c dpa

$(ORIG_TARGZ): quotez
	cp -r $< quotez_$(VERSION)
	cd quotez_$(VERSION) && rm -rf .git
	tar -czf $@ quotez_$(VERSION)
	rm -rf quotez_$(VERSION)

create-source-package: %: %.diff quotez $(ORIG_TARGZ)
	cd quotez && git apply ../$<

quilt-new-patch: %: %.diff quotez $(ORIG_TARGZ) create-source-package
	cd quotez && git apply ../$<

quilt-edit-patch: %: %.diff quotez $(ORIG_TARGZ) quilt-new-patch
	cd quotez && git apply ../$<

binary: quotez
	cd quotez && debuild -b

source: quotez
	cd quotez && debuild -S

clean:
	rm -rf quotez*

.PHONY: create-source-package quilt-new-patch quilt-edit-patch binary source