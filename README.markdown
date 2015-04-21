# term_extract - Term Extract

## Description:

The original Term-extract gem depends on the rbtagger gem for POS recognition.

I noticed a major bug with rbtagger. Under heavy load, ruby reports a buffer overflow, which points at the .so files used by rbtagger. Parallel processing over large document sets was impossible.
This gem eliminates the need for the rbtagger gem, and instead ports the engtagger gem for the POS recognition.

This combination works well under heavy load, and does not report any buffer overflow errors, even when processing over 1.3 million documents, over 12 cpu cores, in a multithreaded sidekiq driven ruby program.
The syntax and all other features remain unchanged.

Term_extract extracts proper nouns (named things like 'Manchester United') and ordinary nouns (like 'event') from text documents.

## Usage:

An example extracting terms from a piece of content:

    require 'term_extract'

    content = <<DOC
    Business Secretary Vince Cable will stay in cabinet despite
    "declaring war" on Rupert Murdoch, says Downing Street.
    DOC

    terms = TermExtract.extract(content)

## Options

The #extract method takes an (optional) options hash, that allows the term extractor behaviour to be modified.  The following options are available:

* min_occurance - The minimum number of times a single word term must occur to be included in the results, default 3
* min_terms - Always include multiword terms that comprise more than @min_terms words, default 2
* types - Extract proper nouns (:nnp) or nouns (:nn) or both (:all), default :all
* include_tags - Include the extracted POS tags in the results, default false
* collapse_terms - Remove shorter terms that are part of larger ones, default true

Sample usage:

    terms = TermExtract.extract(content, :types => :nnp, :include_tags => true)

## Term Extraction Types

By default, the term extractor attempts to extract both ordinary nouns and proper nouns, this behaviour can be configured using the #types option and specifying :all (for both), :nn (for ordinary nouns) or :nnp (for proper nouns).  These codes correspond to the relevent POS tags used during the term extraction process.  Sample usage is shown below:

    terms = TermExtract.extract(content, :types => :nnp)

## Command Line Tool

There is a command line tool that can be used for testing the term extractor.  It is best used in conjunction with another tool to extract the relevent content (e.g. pismo) :

    pismo http://www.bbc.co.uk/news/uk-politics-12085506 body | ruby -rubygems -e 'puts YAML.parse($stdin.read)[:body].value' | ./term-extract nnp | ruby -rubygems -e 'puts YAML.load($stdin.read)'

## Note on Patches/Pull Requests

* Fork the project.
* Make your feature addition or bug fix.
* Add tests for it. This is important so I don't break it in a future version unintentionally.
* Commit, do not mess with Rakefile, version, or history as it's handled by Jeweler.
* Send me a pull request. I may or may not accept it.

## Acknowledgements

The algorithm and extraction code is based on the original python code at:

http://pypi.python.org/pypi/topia.termextract/

## Copyright and License

GPL v3 - See LICENSE.txt for details.
Copyright (c) 2010, Rob Lee

