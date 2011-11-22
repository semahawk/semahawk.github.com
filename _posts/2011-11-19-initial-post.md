---
layout: post
title: "Initial post."
desc: "This is just an initial post. Ignore it, unless you don't want to ignore it then don't ignore it, unless it says something different. Or you could just don't not shouldn't do ignore this but it's highly not recommended."
---
Oh, you're [here](/)! Emmm.. There's nothing yet. As you probably can see by yourself. Be your `paths` straight, good man!

## Code samples

{% highlight ruby %}
# https://github.com/ruby/ruby/blob/trunk/lib/yaml/store.rb

#
# YAML::Store
#
require 'yaml'
require 'pstore'

# YAML::Store provides the same functionality as PStore, except it uses YAML
# to dump objects instead of Marshal.
#
# == Example
#
# require 'yaml/store'
#
# Person = Struct.new :first_name, :last_name
#
# people = [Person.new("Bob", "Smith"), Person.new("Mary", "Johnson")]
#
# store = YAML::Store.new "test.store"
#
# store.transaction do
# store["people"] = people
# store["greeting"] = { "hello" => "world" }
# end
#
# After running the above code, the contents of "test.store" will be:
#
# ---
# people:
# - !ruby/struct:Person
# first_name: Bob
# last_name: Smith
# - !ruby/struct:Person
# first_name: Mary
# last_name: Johnson
# greeting:
# hello: world

class YAML::Store < PStore

  # :call-seq:
  # initialize( file_name, yaml_opts = {} )
  #
  # Creates a new YAML::Store object, which will store data in +file_name+.
  # If the file does not already exist, it will be created.
  #
  #
  # Options passed in through +yaml_opts+ will be used when converting the
  # store to YAML via Hash#to_yaml().
  def initialize file_name, yaml_opts = {}
    @opt = yaml_opts
    super
  end

  # :stopdoc:

  def dump(table)
    YAML.dump @table
  end

  def load(content)
    table = YAML.load(content)
    if table == false
      {}
    else
      table
    end
  end

  def marshal_dump_supports_canonical_option?
    false
  end

  EMPTY_MARSHAL_DATA = YAML.dump({})
  EMPTY_MARSHAL_CHECKSUM = Digest::MD5.digest(EMPTY_MARSHAL_DATA)
  def empty_marshal_data
    EMPTY_MARSHAL_DATA
  end
  def empty_marshal_checksum
    EMPTY_MARSHAL_CHECKSUM
  end
end
{% endhighlight %}

{% highlight js %}
// https://github.com/mootools/mootools-core/blob/master/Source/Fx/Fx.Morph.js

/*
---

name: Fx.Morph

description: Formerly Fx.Styles, effect to transition any number of CSS properties for an element using an object of rules, or CSS based selector rules.

license: MIT-style license.

requires: Fx.CSS

provides: Fx.Morph

...
*/

Fx.Morph = new Class({

	Extends: Fx.CSS,

	initialize: function(element, options){
		this.element = this.subject = document.id(element);
		this.parent(options);
	},

	set: function(now){
		if (typeof now == 'string') now = this.search(now);
		for (var p in now) this.render(this.element, p, now[p], this.options.unit);
		return this;
	},

	compute: function(from, to, delta){
		var now = {};
		for (var p in from) now[p] = this.parent(from[p], to[p], delta);
		return now;
	},

	start: function(properties){
		if (!this.check(properties)) return this;
		if (typeof properties == 'string') properties = this.search(properties);
		var from = {}, to = {};
		for (var p in properties){
			var parsed = this.prepare(this.element, p, properties[p]);
			from[p] = parsed.from;
			to[p] = parsed.to;
		}
		return this.parent(from, to);
	}

});

Element.Properties.morph = {

	set: function(options){
		this.get('morph').cancel().setOptions(options);
		return this;
	},

	get: function(){
		var morph = this.retrieve('morph');
		if (!morph){
			morph = new Fx.Morph(this, {link: 'cancel'});
			this.store('morph', morph);
		}
		return morph;
	}

};

Element.implement({

	morph: function(props){
		this.get('morph').start(props);
		return this;
	}

});
{% endhighlight %}

{% highlight haskell %}
-- A part of https://github.com/archhaskell/cabal2arch/blob/master/Main.hs

{-# LANGUAGE DeriveDataTypeable #-}

-- |
-- Module : cabal2arch: convert cabal packages to Arch Linux PKGBUILD format
-- Copyright : (c) Don Stewart, 2008 .. 2010
-- License : BSD3
--
-- Maintainer: Don Stewart <dons@galois.com>
-- Stability : provisional
-- Portability:
--

-- TODO: if build-type: Configure, accurate C library dependecies
-- require downloading the source, and running configure
--
-- C libraries are dynamically linked, should be listed in depends,
-- rather than makedepends

import Distribution.PackageDescription.Parse
import Distribution.Simple.Utils hiding (die)
import Distribution.Verbosity
import Distribution.Text

-- from the archlinux package:
import Distribution.ArchLinux.PkgBuild
import Distribution.ArchLinux.CabalTranslation

import Control.Monad
import Control.Monad.Error
import qualified Control.Exception as CE

import Data.List

import Text.PrettyPrint

import Paths_cabal2arch
import Data.Version (showVersion)

import System.Directory
import System.Exit
import System.FilePath
import System.IO
import System.Process hiding(cwd)

import System.Console.CmdArgs
import Cabal2Arch.Util

data CmdLnArgs
    = CmdLnConvertOne { argCabalFile :: String, argCreateTar :: Bool, argDataFiles :: String }
    deriving (Data, Typeable)

cmdLnConvertOne :: CmdLnArgs
cmdLnConvertOne = CmdLnConvertOne
    { argCabalFile = "" &= argPos 0 &= typ "FILE|DIR|URL"
    , argCreateTar = False &= name "tar" &= explicit &= help "Create a tar-ball for the source package."
    , argDataFiles = "" &= name "sysinfo" &= typDir &= explicit &= help "Use custom system information files."
    } &= auto &= name "conv" &= help "Convert a single CABAL file."

cmdLnArgs :: CmdLnArgs
cmdLnArgs = modes [cmdLnConvertOne]
    &= program "cabal2arch"
    &= summary ("cabal2arch, v. " ++ showVersion version ++ ": Convert .cabal file to ArchLinux source package")

main :: IO ()
main = cmdArgs cmdLnArgs >>= subCmd
{% endhighlight %}

{% highlight perl %}
# https://github.com/mojombo/jekyll/blob/master/g.pl

#!/usr/bin/perl
#fetch Gravatars

use strict;
use warnings;

use LWP::Simple;
use Digest::MD5 qw(md5_hex);

my $size = 90;
my $output_dir = '.git/avatar';

die("no .git/ directory found in current path\n") unless -d '.git';

mkdir($output_dir) unless -d $output_dir;

open(GITLOG, q/git log --pretty=format:"%ae|%an" |/) or die("failed to read git-log: $!\n");

my %processed_authors;

while(<GITLOG>) {
    chomp;
    my($email, $author) = split(/\|/, $_);

    next if $processed_authors{$author}++;

    my $author_image_file = $output_dir . '/' . $author . '.png';

    #skip images we have
    next if -e $author_image_file;

    #try and fetch image

    my $grav_url = "http://www.gravatar.com/avatar/".md5_hex(lc $email)."?d=404&size=".$size;

    warn "fetching image for '$author' $email ($grav_url)...\n";

    my $rc = getstore($grav_url, $author_image_file);

    sleep(1);

    if($rc != 200) {
        unlink($author_image_file);
        next;
    }
}

close GITLOG;
{% endhighlight %}

## Blockquote

> Wise words, as they truly are wise.
> 
> Or somehow like this.
> 
> -- Wise Man.

## Lists

+ Number one.
+ Number two.
+ Number three.
+ Number five.

1. Number one.
2. Number two.
2. Number three.
2. Number eight.

<dt>Title</dt>
<dd>Description</dd>

<dt>Once</dt>
<dd>Again</dd>

## Headers

# One

## Two

### Three

#### Four

##### Five

###### Six

## Tables

<table>
	<tr>
		<th>One</th>
		<th>Two</th>
		<th>Sixteen</th>
	</tr>
	<tr>
		<td>Five</td>
		<td>Million</td>
		<td>Too much</td>
	</tr>
	<tr>
		<td>Null</td>
		<td>Zero</td>
		<td><code>cat /dev/urandom</code></td>
	</tr>
	<tr>
		<td>Ex</td>
		<td>Be</td>
		<td>Fourdred</td>
	</tr>
</table>
