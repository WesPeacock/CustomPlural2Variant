# CustomPlural2Variant
## CustomField to Variants Example
This repo has a worked example of converting a Custom plural field to Plural variants using the FLEx SFM export and import functions

The files  in the repo are:
- **opl.pl** & **de_opl.pl** scripts to change SFM records from multi-line to one-per-line.
    - **opl.pl** - change # to \_HASH\_ and End-of-line inside the SFM record to #
    - **de_opl** undo **opl.pl**
    - see: <https://github.com/sil-dictionary-lexical-services/Opl_DeOplStub> for details
    - The Piped directory <https://github.com/sil-dictionary-lexical-services/Opl_DeOplStub/tree/master/Piped> contains the code used here.
- **\*.fwbackup** before (used to make the export) & after the import of the variant SFM file.
    - the **after variant import** file doesn't have any Bulk Edits done
- **earBay-etc.db** is the exported SFM file of the FLEx database.
- **earBay-etc-variants-import-settings.map** is the SFM import mapping file.
- **earBay-etc-variants.db** is the modified SFM file. I tested it on WSL (Windows Subsystem for Linux) It is produced from **earBay-etc.db** by the following bash one-liner:

```bash
grep -P '\\(lx|hm|z0) ' earBay-etc.db |perl -pf opl.pl |grep '\\z0 ' |perl -pE 's/#\\hm //;' |perl -pE 's/\\lx (.*?#)(\\z0 )(.*)/\\lx $3\\mn $1\\flg Has_plural#/' |perl -pf de_opl.pl >earBay-etc-variants.db
```
Here's how the one-liner breaks down:

```bash
# select just the lexeme, homograph# and custom plural
grep -P '\\(lx|hm|z0) ' earBay-etc.db
# change:
# \lx earbay
# \hm 1
# \z0 bearsbay
# to
# \lx earbay#\hm 1#\z0 bearsbay#
perl -pf opl.pl
# ignore files without a custom plural field
grep '\\z0 '
# attach homograph# to the lexeme
# \lx earbay#\hm 1# -> \lx earbay1#
perl -pE 's/#\\hm //;'
# make the custom plural the new lexeme
# make the old lexeme a \mn and add a \flg field
perl -pE 's/\\lx (.*?#)(\\z0 )(.*)/\\lx $3\\mn $1\\flg Has_plural#/'
#undo the one-per-line
perl -pf de_opl.pl >earBay-etc-variants.db
```


#### About this Document

This document is written in Markdown format. It's hosted on *github.com*. The github site that it's hosted on will display it in a formatted version.

If you're looking at it another way and you're seeing unformatted text, there are good Markdown editors available for Windows and Linux. An free on-line editor is available at https://stackedit.io/
