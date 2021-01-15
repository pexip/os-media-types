The `media-types` package
=========================

This package distributes the file `/etc/mime.types`.  It is needed mainly by
applications identifying a file's [media type](https://tools.ietf.org/html/rfc6838)
by its filename's extension.  For instance, several web servers reverse depend
on it.  A large number of packages [mention `/etc/mime.types` in their
sources](http://codesearch.debian.net/search?q=%2Fetc%2Fmime.types+&literal=1)

Non-maintainer uploads
----------------------

In principle, please ask before uploading this package.  If a change is urgent
and you are Debian developer, consider a _Team upload_, so that you can
follow the semantic versioning scheme described below.

Semantic versioning
--------------------

The version number of this package follows the [semantic versionning](https://semver.org/)
scheme.  Update the _MAJOR_ number when removing a media type or changing the number of
occurrences of a filename extension.  Update the _MINOR_ number when adding a new
type or filename extension.  Update the _PATCH_ number for other changes.

The IANA folder in the source tree
----------------------------------

contains a copy of the information on the IANA's website at the time I
synchronise from it.

Removal of duplicated file extensions
-------------------------------------

Some tools are not able to handle multiple occurences of a file suffix in
`/etc/mime.types`.  At the moment I am removing duplicated file extensions.  I
might revisit that in the future.

The following oneliner sorts the file extensions by the times they occur.

    grep -v \# mime.types | awk '{$1=""; print}'| perl -pe 's/(\s)+/\n/g' |  sed '/^$/d' | sort | uniq -c | sort -n

Insert `tr A-Z a-z |` before the `sort` command to search for duplicates
that differ only by character case.

### File extensions for media types listed in two related types

 - Script languages such as `sh`, `csh`, `tcl` have been listed for both
   `text/` and `application/` for a long time.  It is not clear what default
   would be best.  In FreeDesktop's _Shared MIME-info_ database, the choice
   differs per language, for instance TCL is `text/tcl` and CSH is
   `application/x-csh`.  The trend for IANA types seems to priviledge `application/`
   types (for instance for Ecmascript).  But do we want to serve shell scripts
   as `application/` types ?

 - `sub` is declared in `text/vnd.dvb.subtitle` and `image/vnd.dvb.subtitle`.
   Should one be prefered over the other ?

 - `3gp`, `3gpp`, `3g2` and `3gpp2` are declared in both `audio` and `video` subtypes.

 - The `otf` and `ttf` font extensions are declared in `font/otf`, `font/sfnt` and
   `font/ttf`.  It is unclear to me if `.otf` files should be exclusively served
   as `font/otf` and vice-versa for `ttf`, and whether to list extensions for
   `font/sfnt`.  Iemoved `otf` in `application/vnd.oasis.opendocument.formula-template`,
   `font/ttf` and `font/sfnt`, and left it in `font/otf`.  I removed `ttf` in `font/ttf`,
   `font/sfnt` and left it in `font/ttf`.

 - I removed `nb` from `application/mathematica` because it is also declared
   for `application/vnd.wolfram.mathematica`, which seems to be more recent.

 - Removed `qcp` from `audio/qcelp` and kept it in `audio/EVRC-QCP`.

 - Removed `loas` from `audio/aac` and kept if in `audio/usac`.

 - Removed `loas` from `audio/aac` and kept if in `audio/usac`.

 - Removed `mp4` from `audio/mp4` and kept it in `video/mp4`.

 - Removed `sub` from both `text/vnd.dvb.subtitle` and
   `text/vnd.dvb.subtitle`.

 - Removed `aa3` and `omg` from `audio/ATRAC-ADVANCED-LOSSLESS`
   and `audio/ATRAC-X`.

 - I have removed `png` from the possible file extensions of
   `image/vnd.mozilla.apng` as it confused software.

 - `application/vnd.cur.` and `text/vnd.curl` both declare the `curl`
   extension.  Fedora gives it to `application/`.  Debian has
   given it to `text/` before.  I leave it with `text/` for the _Bookworm_
   release.

### File extensions used in unrelated media types

 - `dot` is the extension for Graphviz (`text/vnd.graphviz`) and MS-word
   template (application/msword) files.  Note that Graphviz also specifies `gv`
   as a possible file extension. I removed `dot` in `application/msword` and
   left it in `text/vnd.graphviz`.

 - `sdf` is used in `application/vnd.Kinar` and `application/vnd.stardivision.math`.
   The _stardivision_ types are not listed at the IANA.  It it time to remove them?

 - `omg` is listed in all three ATRAC media types.  Is there a reasonable default?

 - `x3d` is declared in `application/vnd.hzn-3d-crossword` and `model/x3d+xml`.

 - `otf` is a file suffix in `application/vnd.oasis.opendocument.formula-template`
    and in `font/` types.

 - Removed `x3d` in `application/vnd.hzn-3d-crossword` and left it in
   `model/x3d+xml`

 - Removed `sdf` from `application/vnd.stardivision.math` and kept it
   in `application/vnd.Kinar`.

 - `cpt` has been listed for `application/mac-compactpro` and
   `image/x-corelphotopaint` in Debian for multiple releases in a row.
   None of them is declared to the IANA.  As they did not seem to trigger
   bugs so far, I am keeping them for the _Bullseye_ release (but may
   delete them later).

### Chemical media types

Debian has been including the unofficial `chemical` type for a long time.
(<https://www.ch.ic.ac.uk/chemime/>, <https://en.wikipedia.org/wiki/Chemical_file_format#The_Chemical_MIME_Project>)

Maybe most of these types should better be registered as `application`?

In any case, they may create conflicts.  Here I track recent changes.

 - Removed `ent` from `chemical/x-ncbi-asn1-ascii` and `chemical/x-pdb`
   (`ent` is declared in `application/xml-external-parsed-entity`).

 - Removed `mmd` from `chemical/x-macromodel-input` (conflict
   with `application/vnd.chipnuts.karaoke-mmd`).

 - Removed `cer` from `chemical/x-cerius` (conflict with
   `application/pkix-cert`).

Other deviations from the IANA
------------------------------

(probably not an exhaustive list)

 - `image/t38` declares no file extension, but Debian had `T38` and Fedora had
   `t38` when I merged its `mime.types` file in Debian's.  At the moment I am
    keeping both.

 - `application/xml-external-parsed-entity` declares `xml` and `ent`, but at
   the moment only `ent` is listed to avoid duplicates.  I also removed all
   suffixes from the `text/` aliases because RFC7303 suggests a preference
   for the `application/` types.

 - Removed `wav` from `audio/L16` for the _Bullseye_ release because
   I am not entirely sure if it can replace `audio/x-wav`.

 - `application/vnd.apple.mpegurl` declares `m3u8` and `m3u`, but RFC8216
   also mentions `audio/mpgegurl` without registering it to the IANA, so to
   reduce last-minute changes in _Bullseye_ I am leaving `m3u` associated with
   `audio/mpegurl`.

 - `pls` is declared in `application/pls+xml`.  Unfortunately, it is also used
   for a different file format served as `audio/x-scpls`.  (See for instance
   <https://github.com/y20k/transistor/issues/124>). As this is the
   media type for which it is associated in _Buster_ I am keeping the
   status quo in _Bullseye_ and removed `pls` from `application/pls+xml`.
   (By the way other file suffixes are missing in `application/pls+xml`).

 - Removed `ica` from `application/vnd.commerce-battelle` because it is used
   in `application/x-ica` (https://wiki.archlinux.org/index.php/citrix)
   (https://support.citrix.com/article/CTX804493).

 - `application/vnd.mif` does not declare a file extension, but `mif`
   appears to be the correct one and was added because it is already
   in Fedora and allows for the removal of `application/x-mif`.
   (<https://help.adobe.com/en_US/framemaker/mifreference/mifref.pdf>)

 - `application/pkix-attr-cert` and `application/vnd.nokia.n-gage.ac+xml`
    declare `ac`, but the N-Gage service appears to be discontinued, therefore
    I removed `ac` to `application/vnd.nokia.n-gage.ac+xml`.

Misc. notes
-----------

## List tab-separated types and subtypes from `mime.types`.

    perl -nE 'next if /^#|^ |^$/ ; say join "\t", (split m,/|\s,)[0,1]' mime.types

## List types and subtypes from the FreeDesktop shared-mime-info project.

    wget http://cgit.freedesktop.org/xdg/shared-mime-info/plain/freedesktop.org.xml.in
    vi freedesktop.org.xml.in  # edit by removing DOCTYPE, removing the <?xml line
                                 and removing the xmlns attribute in the mime-info tag.
    cat freedesktop.org.xml.in |
      xmlstarlet sel -T -t -m "mime-info" \
                           -m "mime-type" -v "@type" -o "$(printf "\t")" \
                           -m "glob" -v "@pattern" -o " " -b -nl |
      sed 's/\*\.//g'

## To do ?

 - Test at build time that there are no duplicated media types in `mime.types`.

## To check for IANA `image` media types that are not yet in `mime.types`

 - wget https://www.iana.org/assignments/media-types/image.csv
 - sed 1d image.csv | cut -f2 -d, | sed '/^$/d' | grep -f - mime.types | cut -f 1 | grep -f - -v image.csv

## Draft oneliner to add lines between content types

 - perl -F/ -ne 'print "\n" if $prev ne $F[0] ; print ; $prev = $F[0]' mime.types

## Other distributiosn

 - Fedora has a [`mailcap`](https://pagure.io/mailcap) package that ships a `mime.types` file,
   a `mailcap` program and its manpage.

 - Arch Linux takes its `mime.types` file from Fedora and distributes it in a
   [`mailcap-mime-types`](https://aur.archlinux.org/packages/mailcap-mime-types/) package.
   It also distributes Debian's `run-mailcap` program in a
   [`run-mailcap`](https://aur.archlinux.org/packages/run-mailcap/) package
   via Ubuntu.  The page on [Default applications](https://wiki.archlinux.org/index.php/Default_applications)
   on the Arch Wiki is (as usual for this wiki) very informative.

 - Ubuntu's Launchpad has a bunch of [open issues](https://bugs.launchpad.net/ubuntu/+source/mime-support),
   some of which might be relevant.
