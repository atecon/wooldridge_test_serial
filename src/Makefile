PKG = wooldridge_test_serial

$(PKG).gfn: $(PKG).inp $(PKG).spec $(PKG)_help.txt $(PKG)_sample.inp
	gretlcli --makepkg $(PKG).inp

#$(PKG).pdf: $(PKG).tex
#	pdflatex $<
#	bibtex $(PKG)
#	pdflatex $<
#	pdflatex $<

#$(PKG).zip: $(PKG).gfn $(PKG).pdf
#	echo makepkg $(PKG).zip | gretlcli -b -

#install: $(PKG).zip
#	echo pkg install $(PKG).zip --local | gretlcli -b -

install: $(PKG).gfn
	echo pkg install $(PKG).gfn --local | gretlcli -b -

clean:
	rm -f $(PKG).zip
