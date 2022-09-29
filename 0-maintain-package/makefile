VERSION=0.2.2
ORIG_TARGZ=quotez_$(VERSION).orig.tar.gz

all: quotez

quotez:
	git clone https://github.com/pseudocc/quotez.git $@
	cd $@ && git checkout tags/v$(VERSION) && git switch -c dpa

$(ORIG_TARGZ):
	git clone https://github.com/pseudocc/quotez.git quotez_$(VERSION)
	cd quotez_$(VERSION) && git checkout tags/v$(VERSION) && rm -rf .git
	tar -czf $@ quotez_$(VERSION)
	rm -rf quotez_$(VERSION)

create-source-package: 0-stage.stamp
0-stage.stamp: create-source-package.diff quotez $(ORIG_TARGZ)
	cd quotez && git apply ../$<
	cd quotez && git add . && git commit -m "create source package"
	touch $@

quilt-new-patch: 1-stage.stamp
1-stage.stamp: quilt-new-patch.diff quotez $(ORIG_TARGZ) create-source-package
	cd quotez && git apply ../$<
	cd quotez && git add . && git commit -m "make a new patch (using quilt)"
	touch $@

quilt-edit-patch: 2-stage.stamp
2-stage.stamp: quilt-edit-patch.diff quotez $(ORIG_TARGZ) quilt-new-patch
	cd quotez && git apply ../$<
	cd quotez && git add . && git commit -m "edit an existing patch (using quilt)"
	touch $@

apply-upstream-changes: 3-stage.stamp
3-stage.stamp: update-versions.diff quotez quilt-edit-patch
	make quotez_0.3.0.orig.tar.gz VERSION=0.3.0
	cd quotez && git rebase tags/v0.3.0 && git apply ../$<
	cd quotez && git add . && git commit -m "apply upstream changes"
	touch $@

binary: quotez
	cd quotez && debuild -b

source: quotez
	cd quotez && debuild -S

clean:
	rm -rf quotez* *.stamp

.PHONY: create-source-package quilt-new-patch quilt-edit-patch apply-upstream-changes binary source
