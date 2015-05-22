# Translate Shell

[![Build Status](https://travis-ci.org/soimort/translate-shell.png)](https://travis-ci.org/soimort/translate-shell)
[![Version](https://raw.githubusercontent.com/soimort/translate-shell/gh-pages/images/badge-release.png)](https://github.com/soimort/translate-shell/releases)
[![Download](https://raw.githubusercontent.com/soimort/translate-shell/gh-pages/images/badge-download.png)](http://www.soimort.org/translate-shell/trans)
[![Gitter](https://badges.gitter.im/Join%20Chat.svg)](https://gitter.im/soimort/translate-shell?utm_source=badge&utm_medium=badge&utm_campaign=pr-badge&utm_content=badge)

**[Translate Shell](http://www.soimort.org/translate-shell)** (formerly _Google Translate CLI_) is a command-line translator powered by **[Google Translate](https://translate.google.com/)**. It gives you easy access to Google Translate in your terminal:

```
$ trans 'Saluton, Mondo!'
Saluton, Mondo!

Hello, World!

Translations of Saluton, Mondo!
[ Esperanto -> English ]
Saluton ,
    Hello,
Mondo !
    World!
```

By default, translations with detailed explanations are shown. You can also translate the text briefly: (only the most relevant translation will be shown)

```
$ trans -brief 'Saluton, Mondo!'
Hello, World!
```

**Translate Shell** can also be used like an interactive shell; input the text to be translated line by line:

```
$ trans -shell -brief
> Rien ne réussit comme le succès.
Nothing succeeds like success.
> Was mich nicht umbringt, macht mich stärker.
What does not kill me makes me stronger.
> Юмор есть остроумие глубокого чувства.
Humor has a deep sense of wit.
> 學而不思則罔，思而不學則殆。
Learning without thought is labor lost, thought without learning is perilous.
> 幸福になるためには、人から愛されるのが一番の近道。
In order to be happy, the best way is to be loved by people.
```

## Prerequisites

### System Requirements

**Translate Shell** is known to work on many POSIX-compliant systems, including but not limited to:

* GNU/Linux
* OS X
* FreeBSD
* Cygwin

### Dependencies

* **[GNU Awk](https://www.gnu.org/software/gawk/)** (**gawk**) **4.0 or later**
    * This program relies heavily on GNU extensions of the [AWK language](http://en.wikipedia.org/wiki/AWK), which are non-portable for other AWK implementations (e.g. nawk).
    * How to get gawk:
      * gawk comes with all GNU/Linux distributions.
      * On FreeBSD, gawk is available in the ports.
      * On OS X, gawk is available in MacPorts and Homebrew.
* **[GNU Bash](http://www.gnu.org/software/bash/)** or **[Zsh](http://www.zsh.org/)**
    * You may use Translate Shell from any Unix shell of your choice (bash, zsh, ksh, tcsh, fish, etc.); however, the wrapper script requires either **bash** or **zsh** installed.

### Optional Dependencies

* **[GNU FriBidi](http://fribidi.org/)**: _an implementation of the Unicode Bidirectional Algorithm (bidi)_
    * required for displaying text in Right-to-Left scripts (e.g. Arabic, Hebrew)
* **[mplayer](http://www.mplayerhq.hu/)**, **[mplayer2](http://www.mplayer2.org/)**, **[mpv](http://mpv.io/)**, **[mpg123](http://mpg123.org/)**, or **[eSpeak](http://espeak.sourceforge.net/)**
    * required for the Text-to-Speech functionality
* **[less](http://www.greenwoodsoftware.com/less/)**, **[more](http://pubs.opengroup.org/onlinepubs/9699919799/utilities/more.html)** or **[most](http://www.jedsoft.org/most/)**
    * required for terminal paging
* **[rlwrap](http://utopia.knoware.nl/~hlub/uck/rlwrap/#rlwrap)**: *a GNU readline wrapper*
    * required for readline-style editing and history in the interactive shell
* **[curl](http://curl.haxx.se/)** with **OpenSSL** support
    * required for secured URL fetching (self-upgrading, etc.)

### Environment and Fonts

It is strongly recommended to use a UTF-8 codeset in your default [locale](http://en.wikipedia.org/wiki/Locale), as it has potential support for all languages. You can check whether your codeset is UTF-8 using the `locale` command.

In addition, you must have corresponding fonts for the language(s) / script(s) you wish to display in your terminal. See **[wiki: Writing Systems and Fonts](https://github.com/soimort/translate-shell/wiki/Writing-Systems-and-Fonts#unicode-fonts)** for more details on scripts and recommended Unicode fonts.

## Try It Out!

Start an interactive shell and translate anything you input into your native language: (in **bash** or **zsh**)

    $ gawk -f <(curl -Ls git.io/translate) -shell

(in **fish**)

    $ gawk -f (curl -Ls git.io/translate | psub) -shell

## Installation

### Option #1. Direct Download

Download [the self-contained executable](http://git.io/trans) and place it into your path. It's everything you need.

    $ wget git.io/trans
    $ chmod +x ./trans

There is a [GPG signature](http://www.soimort.org/translate-shell/trans.sig).

### Option #2. From Git

    $ git clone https://github.com/soimort/translate-shell
    $ cd translate-shell/
    $ make
    $ [sudo] make install

In case you have only zsh but not bash in your system, build with:

    $ make TARGET=zsh

The default `PREFIX` of installation is `/usr/local`. To install the program to somewhere else (e.g. `/usr`, `~/.local`), use:

    $ [sudo] make PREFIX=/usr install

### Option #3. From A Package Manager

On OS X with Homebrew:

    $ brew install http://www.soimort.org/translate-shell/translate-shell.rb

On Linux, you may ignore its dependencies (e.g. gawk) if you already have them in your system:

    $ brew install --ignore-dependencies http://www.soimort.org/translate-shell/translate-shell.rb

See **[wiki: Distros](https://github.com/soimort/translate-shell/wiki/Distros)** on how to install from a specific package manager on your distro.

## Introduction by Examples

### Translate a Word

#### From any language to your language

Google Translate can identify the language of the source text automatically, and Translate Shell by default translates the source text into the language of your `locale`.

    $ trans vorto

#### From any language to one or more specific languages

Translate a word into French:

    $ trans :fr word

Translate a word into Chinese and Japanese: (use a plus sign "`+`" as the delimiter)

    $ trans :zh+ja word

Alternatively, equals sign ("`=`") can be used in place of the colon ("`:`"). Note that in some shells (e.g. zsh), equals signs may be interpreted differently, therefore the argument specifying languages needs to be protected:

    $ trans {=zh+ja} word
    $ trans '=zh+ja' word

You can also use the `-target` (`-t`) option to specify the target language(s):

    $ trans -t zh+ja word

#### From a specific language

Google Translate may wrongly identify the source text as some other language than you expected:

    $ trans 手紙

In that case, you need to specify its language explicitly:

    $ trans ja: 手紙
    $ trans zh: 手紙

You can also use the `-source` (`-s`) option to specify the source language:

    $ trans -s ja 手紙

### Translate Multiple Words or a Phrase

Translate each word alone:

    $ trans en:zh word processor

Put words into one argument, and translate them as a whole:

    $ trans en:zh "word processor"

### Translate a Sentence

Translating a sentence is much the same like translating a phrase; you can just quote the sentence into one argument:

    $ trans :zh "To-morrow, and to-morrow, and to-morrow,"
    $ trans :zh 'To-morrow, and to-morrow, and to-morrow,'

It is also possible to translate multi-line sentences:

    $ trans :zh "Creeps in this petty pace from day to day,
    > To the last syllable of recorded time;
    > And all our yesterdays have lighted fools
    > The way to dusty death."

To avoid punctuation marks (e.g. "`!`") or other special characters being interpreted by the shell, use *single quotes*:

    $ trans :zh 'Out, out, brief candle!'

There are some cases though, you may still want to use *double quotes*: (e.g. the sentence contains a single quotation mark "`'`")

    $ trans :zh "Life's but a walking shadow, a poor player"

### Brief Mode

By default, Translate Shell displays translations in a verbose manner. If you prefer to see only the most relevant translation, there is a brief mode available using the `-brief` (`-b`) option:

    $ trans -b :fr "Saluton, Mondo"

In brief mode, phonetic notation (if any) is not shown by default. To enable this, put an at sign "`@`" in front of the language code:

    $ trans -b :@ja "Saluton, Mondo"

### Dictionary Mode

Google Translate can be used as a dictionary. When translating a word and the target language is the same as the source language, the dictionary entry of the word is shown:

    $ trans :en word

To enable dictionary mode no matter whether the source language and the target language are identical, use the `-dictionary` (`-d`) option.

    $ trans -d fr: mot

**Note:** Not every language supported by Google Translate has provided dictionary data. See **[wiki: Languages](https://github.com/soimort/translate-shell/wiki/Languages)** to find out which language(s) has dictionary support.

### Text-to-Speech

Use the `-play` (`-p`) option to listen to the translation:

    $ trans -b -p :ja "Saluton, Mondo"

### Terminal Paging

Sometimes the content of translation can be too much for display in one screen. Use the `-view` (`-v`) option to view the translation in a terminal pager such as `less` or `more`:

    $ trans -d -v word

### Right-to-Left (RTL) Languages

[Right-to-Left (RTL) languages](http://en.wikipedia.org/wiki/Right-to-left) are well supported via [GNU FriBidi](http://fribidi.org/).

The program will automatically adjust the screen width for padding when displaying right-to-left languages. Alternatively, you may use the `-width` (`-w`) option to specify the screen width:

    $ trans -b -w 40 :he "Saluton, Mondo"

See **[wiki: Languages](https://github.com/soimort/translate-shell/wiki/Languages)** to find out which language(s) uses a Right-to-Left writing system.

### Pipeline, Input and Output

If no source text is given in command-line arguments, the program will read from standard input, or from the file specified by the `-input` (`-i`) option:

    $ echo "Saluton, Mondo" | trans -b :fr
    $ trans -b -i input.txt :fr

Translations are written to standard output, or to the file specified by the `-output` (`-o`) option:

    $ echo "Saluton, Mondo" | trans -b -o output.txt :fr

### Translate a File

Instead of using the `-input` option, a [file URI scheme](http://en.wikipedia.org/wiki/File_URI_scheme) (`file://` followed by the file name) can be used as a command-line argument:

    $ trans :fr file://input.txt

**Note**: Brief mode is used when translating from file URI schemes.

### Translate a Web Page

To translate a web page, an http(s) URI scheme can be used as an argument:

    $ trans :fr http://www.w3.org/

A browser session will open for viewing the translation (via Google Translate's web interface). To specify your web browser of choice, use the `-browser` option:

    $ trans -browser firefox :fr http://www.w3.org/

### Interactive Translate Shell (REPL)

Start an interactive shell using the `-shell` (or `-interactive`, `-I`) option:

    $ trans -shell

You may specify the source language and the target language(s) before starting an interactive shell:

    $ trans -shell en:fr

You may also change these settings during an interactive session. See **[wiki: REPL](https://github.com/soimort/translate-shell/wiki/REPL)** for more advanced usage of the interactive Translate Shell.

## Advanced Usage

* **[REPL](https://github.com/soimort/translate-shell/wiki/REPL)**
* **[Text Editor Integration](https://github.com/soimort/translate-shell/wiki/Text-Editor-Integration)**
* **[Configuration](https://github.com/soimort/translate-shell/wiki/Configuration)**
* **[Themes](https://github.com/soimort/translate-shell/wiki/Themes)**
