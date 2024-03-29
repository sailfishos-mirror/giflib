# Regression-test suite for the giflib library and tools
# All utilities have tests except gifbg, gifcolor, and gifhisto.

.SUFFIXES: .gif .rgb

# This is what to do by default
test:
	@$(MAKE) --quiet tap | ./tapview

tap:
	@$(MAKE) --quiet tapstream | tee taplog$$$$; echo "1..$$(grep ok  taplog$$$$ | wc -l)"; rm taplog$$$$

tapstream: render-regress \
	gif2rgb-regress \
	gifbuild-regress \
	gifclrmp-regress \
	gifecho-regress \
	giffilter-regress \
	giffix-regress \
	gifinto-regress \
	gifsponge-regress \
	giftext-regress \
	giftool-regress \
	gifwedge-regress

rebuild: render-rebuild \
		gif2rgb-rebuild \
		gifclrmp-rebuild \
		gifecho-rebuild \
		giffix-rebuild \
		gifinto-rebuild \
		giftext-rebuild \
		gifwedge-rebuild

UTILS = ..
PICS = ../pic

GIFS := $(shell ls ../pic/*.gif)

# Test decompression and rendering by unpacking images,
# converting them to RGB, and comparing that result to a check file.
render-regress:
	@for test in $(GIFS); \
	do \
	    stem=`basename $${test} | sed -e "s/.gif$$//"`; \
	    $(UTILS)/gif2rgb -1 <$${test} | ./cmpdiffer "gif2rgb: test of $${test}" $${stem}.rgb; \
	done
render-rebuild:
	@for test in $(GIFS); do \
		stem=`basename $${test} | sed -e "s/.gif$$//"`; \
		echo "Remaking $${stem}.rgb"; \
		$(UTILS)/gif2rgb -1 -o $${stem}.rgb $${test}; \
	done

gif2rgb-regress:
	@$(UTILS)/gif2rgb -c 3 -s 100 100 <gifgrid.rgb | $(UTILS)/gifbuild -d | sed "/sort flag/s/off/on/" | ./tapdiffer "checking gif2rgb idempotency" gifgrid.ico
gif2rgb-rebuild:
	@echo "Rebuilding gif2rgb checkfile."
	@$(UTILS)/gif2rgb -c 3 -s 100 100 <gifgrid.rgb | $(UTILS)/gifbuild -d >gifgrid.ico

gifbuild-regress:
	@$(UTILS)/gifbuild -d <$(PICS)/treescap.gif | ./tapdiffer "gifbuild: basic sanity check" treescap.ico
	@$(UTILS)/gifbuild <$(PICS)/sample.ico | $(UTILS)/gifbuild -d > $@.sample-1.ico; $(UTILS)/gifbuild < $@.sample-1.ico | $(UTILS)/gifbuild -d > $@.sample-2.ico;
	@./tapdiffer <$@.sample-1.ico "gifbuild: Checking idempotency on an icon file." $@.sample-2.ico; rm $@.sample-?.ico
	@$(UTILS)/gifbuild -d <$(PICS)/fire.gif > $@.fire1.ico
	@$(UTILS)/gifbuild < $@.fire1.ico > $@.fire2.gif
	@$(UTILS)/gifbuild -d < $@.fire2.gif > $@.fire2.ico
	@./tapdiffer <$@.fire1.ico "gifbuild: Checking idempotency on an animation." $@.fire2.ico
	@rm -f $@.fire1.ico  $@.fire2.ico $@.fire2.gif

gifclrmp-regress:
	@for test in $(GIFS); \
	do \
	    stem=`basename $${test} | sed -e "s/.gif$$//"`; \
	    if ! $(UTILS)/gifclrmp <$${test} > $@.$${stem}.regress 2>&1; \
	    then \
		echo "not ok - gifclrmap: crashed on $${test}"; \
	    else \
		./tapdiffer <$@.$${stem}.regress "gifclrmap: Checking colormap of $${test}" $${stem}.map; \
	    fi \
	done
	@rm -f $@.*.regress
gifclrmp-rebuild:
	@for test in $(GIFS); do \
		stem=`basename $${test} | sed -e "s/.gif$$//"`; \
		echo "Remaking $${stem}.map"; \
		$(UTILS)/gifclrmp <$${test} >$${stem}.map; \
	done

gifecho-rebuild:
	@echo "Rebuilding gifecho test."
	@$(UTILS)/gifecho -t "foobar" | $(UTILS)/gifbuild -d >foobar.ico
gifecho-regress:
	@$(UTILS)/gifecho -t "foobar" | $(UTILS)/gifbuild -d | ./tapdiffer "gifecho: Testing gifecho behavior" foobar.ico

giffilter-regress:
	@for test in $(GIFS); \
	do \
	    stem=`basename $${test} | sed -e "s/.gif$$//"`; \
	    $(UTILS)/giffilter <$${test} | $(UTILS)/gif2rgb | ./cmpdiffer "giffiltr: copy of $${test}" "$${stem}.rgb"; \
	done

giffix-rebuild:
	@echo "Rebuilding giffix test."
	@head --bytes=-20 <$(PICS)/treescap.gif | $(UTILS)/giffix 2>/dev/null | $(UTILS)/gifbuild -d >giffixed.ico
giffix-regress:
	@head --bytes=-20 <$(PICS)/treescap.gif | $(UTILS)/giffix 2>/dev/null | $(UTILS)/gifbuild -d | ./tapdiffer "giffix: Testing giffix behavior" giffixed.ico

gifinto-regress:
	@rm -f $@.giflib.tmp
	@$(UTILS)/gifinto <$(PICS)/porsche.gif $@.giflib.tmp
	@if test ! -f $@.giflib.tmp; then echo "not ok - gifinto failed to create a file when it should have."; fi
	@rm -f $@.giflib.tmp
	@echo "0123456789" | $(UTILS)/gifinto $@.giflib.tmp 2>/dev/null
	@if test -f $@.giflib.tmp; \
	then \
		echo "not ok - gifinto created a file when it shouldn't have."; \
	else \
		echo "ok - gifinto correctly refrained from file creation."; \
	fi
	@rm -f $@.giflib.tmp

gifsponge-regress:
	@for test in $(GIFS); \
	do \
	    stem=`basename $${test} | sed -e "s/.gif$$//"`; \
	    $(UTILS)/gifsponge <$${test} | $(UTILS)/gif2rgb | ./cmpdiffer "gifsponge: sink of $${test}" $${stem}.rgb; \
	done

giftext-regress:
	@for test in $(GIFS); \
	do \
	    stem=`basename $${test} | sed -e "s/.gif$$//"`; \
	    $(UTILS)/giftext <$${test} | ./cmpdiffer "text dump of $${test}" $${stem}.dmp; \
	done
giftext-rebuild:
	@for test in $(GIFS); do \
		stem=`basename $${test} | sed -e "s/.gif$$//"`; \
		echo "Remaking $${stem}.dmp"; \
		$(UTILS)/giftext <$${test} >$${stem}.dmp; \
	done

giftool-regress:
	@$(UTILS)/giftool <$(PICS)/gifgrid.gif | $(UTILS)/gif2rgb | ./cmpdiffer "giftool: expensive copy via giftool" gifgrid.rgb
	@$(UTILS)/giftool -i on <$(PICS)/treescap-interlaced.gif | $(UTILS)/gif2rgb | ./cmpdiffer "giftool: deinterlace" treescap.rgb
	@$(UTILS)/giftool -i off <$(PICS)/treescap.gif | $(UTILS)/gif2rgb | ./cmpdiffer "giftool: interlace" treescap-interlaced.rgb

gifwedge-rebuild:
	@echo "Remaking the gifwedge test."
	@$(UTILS)/gifwedge >wedge.gif
gifwedge-regress:
	@$(UTILS)/gifwedge | ./cmpdiffer "gifwedge: wedge generation." wedge.gif
