Pandoc Scholar
==============

[![release shield]](https://github.com/pandoc-scholar/pandoc-scholar/releases)
[![DOI]](https://zenodo.org/badge/latestdoi/82204858)
[![license shield]](./LICENSE)
[![build status]](https://travis-ci.org/pandoc-scholar/pandoc-scholar)

Create beautiful, semantically enriched articles with pandoc. This package
provides utilities to make publishing of scientific articles as simple and
pleasant as possible. It simplifies setting authors' metadata in YAML blocks,
allows to add semantic annotation to citations, and only requires the programs
pandoc and make. Pandoc Scholar is described in the [paper] published in PeerJ
Computer Science.

[release shield]: https://img.shields.io/github/release/pandoc-scholar/pandoc-scholar.svg
[license shield]: https://img.shields.io/github/license/pandoc-scholar/pandoc-scholar.svg
[build status]:   https://img.shields.io/travis/pandoc-scholar/pandoc-scholar/master.svg
[DOI]: https://zenodo.org/badge/82204858.svg
[paper]: https://peerj.com/articles/cs-112/

Prerequisites
-------------

This package builds on [pandoc](http://pandoc.org/), the universal document
converter. See the pandoc website for
[installation instructions](http://pandoc.org/installing.html) and
suggestions for LaTeX packages, which we use for PDF generation.


Installation
------------

Archives containing all required files are provided for each release. Use the
*release* button above and download a `pandoc-scholar` archive; both archive
files, `.zip` and `.tar.gz`, contain the same files, choose the one most
convenient to you.

A `pandoc-scholar` folder will be created on unpacking. The folder contains all
required scripts and templates.


Usage
-----

### Quickstart

Run `make` to convert the example article into all supported output formats. The
markdown file used to create the output files can be configured via the
`ARTICLE_FILE` variable, either directly in the Makefile or by specifying the
value on the command line.

    make ARTICLE_FILE=your-file.md

### Includable Makefile

The *Makefile*, which does most of the work, is written in a style that makes it
simple to include it from within other Makefiles. This method allows to keep
`pandoc-scholar` installed in a central location and to use the same instance
for multiple projects. The `ARTICLE_FILE` and `PANDOC_SCHOLAR_PATH` variables
must be defined in the including Makefile:

``` Makefile
ARTICLE_FILE        = your-file.md
PANDOC_SCHOLAR_PATH = ../path-to-pandoc-scholar-folder
include $(PANDOC_SCHOLAR_PATH)/Makefile
```

Calling `make` as usual will create all configured output formats. Per default,
this creates *pdf*, *latex*, *docx*, *odt*, *epub*, *html*, and *jats* output.
The set of output files can be reduced by setting the `DEFAULT_EXTENSIONS`
variable to a subset of the aforementioned formats.

Alternative template files can be set using `TEMPLATE_FILE_<FORMAT>` variables,
where `<FORMAT>` is one of *HTML*, *EPUB*, *JATS*, or *LATEX*. The reference
files for ODT and DOCX output can be changed using `ODT_REFERENCE_FILE` and
`DOCX_REFERENCE_FILE`, respectively.

Additional pandoc options can be given on a per-format basis using
`PANDOC_<FORMAT>_OPTIONS` variables. The following uses an actual Makefile as an
example to demonstrate usage of those options.

``` Makefile
ARTICLE_FILE        = open-science-formatting.md

PANDOC_LATEX_OPTIONS  = --latex-engine=xelatex
PANDOC_LATEX_OPTIONS += --csl=peerj.csl
PANDOC_LATEX_OPTIONS += --filter=pandoc-citeproc
PANDOC_LATEX_OPTIONS += -M fontsize=10pt
PANDOC_LATEX_OPTIONS += -M classoption=fleqn

PANDOC_HTML_OPTIONS   = --toc
PANDOC_EPUB_OPTIONS   = --toc

DOCX_REFERENCE_FILE   = pandoc-manuscript.docx
ODT_REFERENCE_FILE    = pandoc-manuscript.odt
TEMPLATE_FILE_LATEX   = pandoc-peerj.latex

PANDOC_SCHOLAR_PATH = pandoc-scholar
include $(PANDOC_SCHOLAR_PATH)/Makefile
```


Metadata Features
-----------------

Pandoc scholar supports additional functionality via metadata fields. Most
notably, the augmentation of articles with author and affiliation data, which is
essential for academic publishing, is greatly simplified when using pandoc
scholar.

### Authors and affiliations

Most metadata should be specified in the YAML block at the top of the article.
Author data and affiliations are taken from the *author* and *institute* field,
respectively. Institutes can be given via user-defined abbreviations, saving
unnecessary repetiontions while preserving readability.

Example:

``` yaml
author:
  - James Dewey Watson:
      institute: cavendish
  - Francis Harry Compton Crick:
      institute: cavendish
institute:
  - cavendish: Cavendish Laboratory, Cambridge
```

Authors are given in the order in which they are listed, while institute order
follows from author order.

The separate institute field may add unwanted complexity in some cases. It is
hence possible to omit it and to give the affiliations name directly in the
author entry:

``` yaml
author:
  - John MacFarlane:
      institute: University of California, Berkeley
```

### Institute address

Often it is not enough to give just a name for institutes. It is hence possible
to add arbitrary fields. The name must then explicitly be set via the *name*
field of the institute entry:

``` yaml
author:
  - Robert Winkler:
      institute: cinvestav
institute:
  - cinvestav:
      name: 'CINVESTAV Unidad Irapuato, Department of Biochemistry and Biotechnology'
      address: 'Km. 9.6 Libramiento Norte Carr. Irapuato-León, 36821 Irapuato Gto. México'
      phone: +52 (462) 623 9635
```

Currently only the institute's address is used in the default template, but
future extensions will be based on this convention.

### Semantic citations

Understanding the reason a citations is included in scholarly articles usually
requires natural language processing of the article. However, navigating the
current literature landscape can be improved and by having that information
accesible and in a machine-readable form. Pandoc scholar supports the CiTO
ontology, allowing authors to specify important meta-information on the citation
directly while writing the text. The property is simply prepended to the
citation key, separated by a colon: `@<property>:citationKey`.

The following table contains all supported keywords and the respective CiTO
properties. Authors are free to use the short-form, the full-length property, or
any of the alternatives listed below.

CiTO property                 | Keyword      | alternatives
----------------------------- | ------------ | ---------------------
agrees\_with                  | agrees\_with | agree\_with
cites                         |              |
cites\_as\_authority          | authority    | as\_authority
cites\_as\_evidence           | evidence     | as\_evidence
cites\_as\_metadata\_document | metadata     | as\_metadata_document
includes\_excerpt\_from       | excerpt      | excerpt\_from
includes\_quotation\_from     | quotation    | quotation\_from
obtaines\_background\_from    | background   | background\_from
uses\_method\_in              | method       | method\_in

Example:

    DNA strands form a double-helix [@evidence:watson_crick_1953].


License
-------

Copyright © 2016–2017 Albert Krewinkel and Robert Winkler except for the
following components:

- HTML template: © 2016 Andrew G. York and Diana Mounter
- JATS support: © 2015–2016 Martin Fenner

This program is free software; you can redistribute it and/or modify it under
the terms of the GNU General Public License as published by the Free Software
Foundation; either version 2 of the License, or (at your option) any later
version.

This program is distributed in the hope that it will be useful, but WITHOUT ANY
WARRANTY; without even the implied warranty of MERCHANTABILITY or FITNESS FOR A
PARTICULAR PURPOSE. See the GNU General Public License for more details.

You should have received a copy of the GNU General Public License along with
this program; if not, write to the Free Software Foundation, Inc., 51 Franklin
Street, Fifth Floor, Boston, MA 02110-1301 USA.
