The `media-types` package
=========================

This package distributes the file `/etc/mime.types`.  It is needed mainly by
applications identifying a file's [media type](https://tools.ietf.org/html/rfc6838)
by its filename's extension.  For instance, several web servers reverse depend
on it.  A large number of packages [mention `/etc/mime.types` in their
sources](http://codesearch.debian.net/search?q=%2Fetc%2Fmime.types+&literal=1).

Non-maintainer uploads
----------------------

In principle, please ask before uploading this package.  If a change is urgent
or if it is only adding a new IANA-registered type, and you are Debian developer,
consider a _[team upload](https://wiki.debian.org/TeamUpload)_,
so that you can follow the semantic versioning scheme described below.

Semantic versioning
--------------------

The version number of this package follows the [semantic versioning](https://semver.org/)
scheme.  Update the _MAJOR_ number when removing a media type or changing the number of
occurrences of a filename extension.  Update the _MINOR_ number when adding a new
type or filename extension.  Update the _PATCH_ number for other changes.

Synchronisation with the IANA
-----------------------------

The `IANA` folder in the source tree contains a copy of the information on the
[IANA's registry](https://www.iana.org/assignments/media-types) at the time I
synchronise from it.

To refresh the information in the `IANA` folder:

    for file in *csv ; do wget -N https://www.iana.org/assignments/media-types/$file ; done

To show IANA media-registered types that are not yet in `mime.types` (example with
the _image_ type).

    for file in IANA/*csv; do
      sed 1d $file | cut -f2 -d, | sed '/^$/d' | grep -f - mime.types | cut -f 1 | grep -f - -v $file
    done |
       grep -ve DEPRECATED -e OBSOLETE -e ,, -e Name,Template,Reference

To show media types in `mime.types` that are not registered to the IANA:

    for file in IANA/*csv; do
        grep ^$(basename $file .csv) mime.types | grep -v ,, | cut -f1 | grep -f - $file | cut -d, -f2 | grep -f - -v mime.types | grep ^$(basename $file .csv)
    done

Removal of duplicated file extensions
-------------------------------------

Some tools are not able to handle multiple occurrences of a file suffix in
`/etc/mime.types`.  At the moment I am removing duplicated file extensions.  I
might revisit that in the future.

The following oneliner shows the duplicated file extensions by the times they occur.

    grep -v \# mime.types | awk '{$1=""; print}'| perl -pe 's/(\s)+/\n/g' |  sed '/^$/d' | sort | uniq -c | sort -n | awk '$1 > 1'

Insert `tr A-Z a-z |` before the `sort` command to search for duplicates
that differ only by character case.

### File extensions for media types listed in two related types

 - Script languages such as `sh`, `csh`, `tcl` have been listed for both
   `text/` and `application/` for a long time.  It is not clear what default
   would be best.  In FreeDesktop's _Shared MIME-info_ database, the choice
   differs per language, for instance TCL is `text/tcl` and CSH is
   `application/x-csh`.  The trend for IANA types seems to privilege `application/`
   types (for instance for ECMAScript).  But do we want to serve shell scripts
   as `application/` types ?

 - `3gp`, `3gpp`, `3g2` and `3gpp2` are declared in both `audio` and `video` subtypes.

 - `sms` is declared in both `application/vnd.3gpp.sms` and `application/vnd.3gpp2.sms`;
   kept the 2nd one.

 - The `otf` and `ttf` font extensions are declared in `font/otf`, `font/sfnt` and
   `font/ttf`.  It is unclear to me if `.otf` files should be exclusively served
   as `font/otf` and vice-versa for `ttf`, and whether to list extensions for
   `font/sfnt`.  I removed `otf` in `application/vnd.oasis.opendocument.formula-template`,
   `font/ttf` and `font/sfnt`, and left it in `font/otf`.  I removed `ttf` in `font/ttf`,
   `font/sfnt` and left it in `font/ttf`.

 - I removed `nb` from `application/mathematica` because it is also declared
   for `application/vnd.wolfram.mathematica`, which seems to be more recent.

 - Removed `qcp` from `audio/qcelp` and kept it in `audio/EVRC-QCP`.

 - Removed `loas` from `audio/aac` and kept if in `audio/usac`.

 - Removed `loas` from `audio/aac` and kept if in `audio/usac`.

 - Removed `mp4` from `audio/mp4` and kept it in `video/mp4`.

 - Added the unregistered extension `m4a` to `audio/mp4`.

 - Removed `sub` from both `text/vnd.dvb.subtitle` and
   `image/vnd.dvb.subtitle`.

 - Removed `aa3` and `omg` from `audio/ATRAC-ADVANCED-LOSSLESS`
   and `audio/ATRAC-X`.

 - When adding `image/avif` I left out the `heif` and `heifs` file extensions,
   that were already used in `image/heif` and `image/heifs`.

 - `application/vnd.cur.` and `text/vnd.curl` both declare the `curl`
   extension.  Fedora gives it to `application/`.  Debian has
   given it to `text/` before.  I leave it with `text/` for the _Bookworm_
   release.

 - `xdw`, `xbd`, `xct`, related to DocuWorks, were declared first in
   `application/vnd.fujixerox.docuworks*` types and again in
    `application/vnd.fujifilm.fb.docuworks`.  I gave priority to the
   fist declaration.

 - `p21`, `stp`, `step`, `stpnc`, and `210` are listed in `application/p21`
   and `model/step`.  I gave priority to `model/step` for `stp` and `step`
   for consistency with other `model/step*` types, and gave the remaining
   extensions to `application/p21`.

 - `stpz` is listed in `application/p21+zip` and `model/step+zip`.  I gave
   priority to the second, for consistency with other `model/step*`.

 - `omg` is listed in all three ATRAC media types.  Is there a reasonable default?

### File extensions used in unrelated media types

 - `aml` is used in `application/AML` and `application/automationml-aml+xml`.
   I kept it in `application/AML` (first arrived, first served).

 - `dot` is the extension for Graphviz (`text/vnd.graphviz`) and MS-word
   template (application/msword) files.  Note that Graphviz also specifies `gv`
   as a possible file extension. I removed `dot` in `application/msword` and
   left it in `text/vnd.graphviz`.

 - `gml` is declared in `application/gml+xml` and `text/vnd.gml`.  I kept
   it in `application/gml+xml` (first arrived, first served).

 - `otf` is a file suffix in `application/vnd.oasis.opendocument.formula-template`
    and in `font/` types.

 - Removed `x3d` in `application/vnd.hzn-3d-crossword` and left it in
   `model/x3d+xml`

 - `cpt` has been listed for `application/mac-compactpro` and
   `image/x-corelphotopaint` in Debian for multiple releases in a row.
   None of them is declared to the IANA.  As they did not seem to trigger
   bugs so far, I am keeping them for the _Bullseye_ release (but may
   delete them later).

 - `pyo` is declared in `model/vnd.pytha.pyox`, but it acknowledges that Python
   also uses this extension, and indeed it is currently in listed under
   `application/x-python-code`, so I did not add it to `model/vnd.pytha.pyox`.

 - Extensions representing a generic format such as `json` or `h5` are not
   listed for media types that merely use the format it even if they declare
   its extension.

 - `ots` is already declared by `application/vnd.oasis.opendocument.spreadsheet-template`,
   so I am not listing it for `application/vnd.opentimestamps.ots`

 - `iso` and `img` are too generic, so although declared in `application/vnd.efi.img`
   and `application/vnd.efi.iso`, I did not list them.

 - `mpp` has been used for a long time by MS-Project (`application/vnd.ms-project`)
   and is listed in `application/dash-patch+xml`.  Priority to the first.

 - `model/prc` was introduced in 2022 and declared the `prc` suffix, which was
   already used in `application/vnd.palm`.  Since Palm OS is discontinued for more than
   10 years, I gave priority to `model/prc`.

 - `sig` is listed in `application/rpki-checklist` and `application/pgp-signature`.
   I gave priority to `application/pgp-signature` as it was the first to use
   this extension.

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

 - In the description o `application/vnd.paos.xml` on the IANA website, the
   subtype is said to be `vnd.paos+xml`.  As I do not know where the error
   is, I am not correcting the current entry (with the +) in Debian.

 - The IANA entry for `application/x-x509-ca-cert` does not declare `crt` as
   a file extension, but the `mime.types` file does for historical reasons.

 - `application/vnd.cncf.helm.chart.provenance.v1.prov` declares `Text` as
   a file extension, but I did not list it because it is too generic.  The
   file contains _“a GnuPG detached ASCII-armored signature”_.

 - I removed `image/vnd.mozilla.apng` as it is obsoleted by `image/apng`.


Media types not registered to the IANA
--------------------------------------

I have been removing some media types not registered to the IANA.  First, those
that do not declare file extensions.  Second, those that have no evidence of
being used, after checking search engines (DuckDuckGo and Google),
codesearch.debian.net, the source tree of the 'file' package, and the
shared-mime-info database (via `/usr/share/mime`).  In case of `*/x-ms-*` type
I also search Google with "type/subtype site:microsoft.com" to be sure.

Misc. notes
-----------

## Sort order

 - `mime.types` is sorted case-insensitively with the `C` locale.
   (`LC_ALL=C sort -k1,1f -t' '`).

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

## Draft oneliner to add lines between content types

 - perl -F/ -ne 'print "\n" if $prev ne $F[0] ; print ; $prev = $F[0]' mime.types

## Other distributions

 - Fedora has a [`mailcap`](https://pagure.io/mailcap) package that ships a `mime.types` file,
   a `mailcap` program and its manpage.

 - Arch Linux takes its `mime.types` file from Fedora and distributes it in a
   [`mailcap-mime-types`](https://aur.archlinux.org/packages/mailcap-mime-types/) package.
   It also distributes Debian's `run-mailcap` program in a
   [`run-mailcap`](https://aur.archlinux.org/packages/run-mailcap/) package
   via Ubuntu.  The page on [Default applications](https://wiki.archlinux.org/index.php/Default_applications)
   on the Arch Wiki is (as usual for this wiki) very informative.

 - It might be useful to check [Ubuntu's open issues](https://bugs.launchpad.net/ubuntu/+source/media-types)
   from time to time.
