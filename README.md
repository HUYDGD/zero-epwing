# Zero-EPWING #

Zero-EPWING is a tool built to export easy to process JSON formatted UTF-8 data from dictionaries in
[EPWING](https://ja.wikipedia.org/wiki/EPWING) format. This is a terrible format for many reasons, some of which are
outlined below:

*   It is based on a closed and undocumented standard.
*   Not well supported as it isn't used anywhere else in the world.
*   The only library for parsing this format, `libeb`, is abandoned.
*   Data is stored in an inconsistent manner, with lots of duplication.
*   Text data is represented using the annoying EUC-JP encoding.
*   Characters which cannot be encoded are represented by image bitmaps.

Most applications that parse EPWING data traditionally use `libeb` to perform dictionary searches in place; dealing with
quirks in the format and `libeb` output is just part of the process. Zero-EPWING takes a different approach -- extract
all the data and output it an sane intermediate format, like JSON. As everyone knows how to parse JSON, it is trivial to
take this intermediate data and store it in a reasonable, industry standard representation.

![](https://foosoft.net/projects/zero-epwing/img/zero-wing.png)

## Requirements ##

*   [Autotools](https://www.gnu.org/software/automake/manual/html_node/Autotools-Introduction.html)
*   [CMake](https://cmake.org/)
*   [GCC](https://gcc.gnu.org/)
*   [Make](https://www.gnu.org/software/make/)
*   [MinGW](http://www.mingw.org/) (Windows only)

## Building ##

The instructions below are for building on Linux, Mac OS X, and Windows, provided your environment is already configured
for development:

1.  Clone the repository by executing `git clone https://github.com/FooSoft/zero-epwing`.
2.  Initialize the git submodules by executing `git submodule init`.
3.  Pull down the submodule data by executing `git submodule update`.
4.  Switch to the `eb` directory and execute `./configure --disable-shared --disable-ebnet --disable-nls`.
5.  While still in the `eb` directory, build the modified library by executing `make`.
6.  Switch to the `jansson` directory then execute `cmake .` and `make`.
7.  From the project root directory, execute `cmake .` and `make`.

## Usage ##

Zero-EPWING takes a single parameter, the directory of the EPWING dictionary to dump. It also supports the following
optional flags:

*   `--entries` (`-e`): output dictionary entry data (most common option).
*   `--fonts` (`-f`): output output font bitmap data (useful for OCR).
*   `--markup` (`-m`): markup the output with as much metadata as possible.
*   `--positions` (`-s`): output *page* and *offset* data for each entry.
*   `--pretty` (`-p`): output pretty-printed JSON (useful for debugging).

Upon loading and processing the requested EPWING data, Zero-EPWING will output a UTF-8 encoded JSON file to `stdout`.
Diagnostic information about errors will be printed to `stderr`. Serious errors will result in this application
returning a non-zero exit code. A sample of the JSON dictionary entry data output is pretty-printed below for reference.

```
{
    "charCode": "jisx0208",
    "discCode": "epwing",
    "subbooks": [
        {
            "title": "大辞泉",
            "copyright": "CD-ROM版大辞泉　1997年4月10日 第1版発行\n\n監　修　松村　明\n発行者　鈴木俊彦\n発行所...
            "entries": [
                {
                    "heading": "亜",
                    "text": "亜\n［音］ア\n［訓］つ‐ぐ\n［部首］二\n［総画数］７\n［コード］区点...
                },
                {
                    "heading": "あ",
                    "text": "あ\n{{w_50275}}\n{{w_50035}}五十音図ア行の第一音。五母音の一。後舌の開母音...
                },
                ...
```

You may have noticed the unusual-looking double curly bracket markers such as `{{w_50035}}`. Remember what I mentioned
about certain characters being represented by image files? There are two graphical fonts sets in each dictionary: narrow
and wide. Both of these font sets are available in four sizes: 24, 30, 36, and 48 pixels. Whenever a character cannot be
encoded as text, a glyph is used in its place. These font indices cannot be converted directly to characters, differ
from one dictionary to another, and must be manually mapped to Unicode character tables. Zero-EWPING has no facility to
map these font glyphs to Unicode by itself, and instead places inline markers in the form of `{{w_xxxx}}` and
`{{n_xxxx}}` in the output, specifying the referenced indices of the wide or narrow fonts respectively.

The bitmaps for these font glyphs can be dumped by executing this application with the `--fonts` command line argument.

## License ##

MIT
