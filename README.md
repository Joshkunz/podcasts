**UNMAINTAINED** This program is in the process of a brutally slow re-write
that is unfortunately not high on my priority list at the moment.

podcasts
========

Shell based podcatcher.

# background
There are a lot of podcatchers out there. A lot of them. The problem
with most of them is twofold. First, oftentimes they're integrated into
a player of some sort, and I often either don't like the podcatcher,
and hate the player, or love the player and hate the podcatcher. Secondly
having and entirely separate application to download podcasts, as well
as play them seemed way more complicated than it needed to be. Thus
podcasts. The goal of this podcatcher is to attempt to separate the
'catcher' and 'player' portions into two separate (hopefully as
lightweight as possible) programs. So that you can get the best
podcatcher, the best player, and all of the benefits a command line
application has to offer.

# installing
I haven't packaged it up yet so just a standard

    pip install -r requirements.txt

should satisfy the dependencies, then you can just link the 'podcasts'
file in whatever manner you please. For example:

    ln -s podcasts ~/bin

# configuration
There are a few options you will need to configure before you can
start using it. First, where your podcasts and feeds should be stored.
You can do that in a `~/.podcastrc` file as shown:

    # where all podcast information is stored
    # all paths are relative to here
    root_dir = "~/podcasts"

    # name of the folder to store cached feeds in (relative to root_dir)
    feed_store = "feeds"

    # name of the folder to store downloaded episodes in (relative to root_dir)
    episode_store = "episodes"

The values provided above are the defaults. If no `~/.podcastrc` file is
found, those values will be used. It's probably also worth noting that
file is just an ordinary python file, so you can use the full python
syntax in that file if you wish.

Next inside of whatever root\_dir you specified previously (or `~/podcasts`
by default) you need a feeds.yaml file, with podcast name, url pairs like
so:

    ---
    99percent: http://invisible99.podbean.com/feed
    radiolab: http://feeds.wnyc.org/radiolab
    ...

With however many podcasts you want. (note the `...` at the end of the file
is required.) If you have a list of your podcasts in opml format, you can
use the supplied opml2feeds and have it generate a feeds.yaml file from
your existing opml file.

# using it
First you need to update your feeds so run:
```bash
$ podcasts update
```
to update all of your podcasts feeds.

Then you can simply run:
```bash
$ podcasts download
```
to download all of the most recent episodes.

For both of these commands you can supply podcast names to make them 
only fetch certain podcasts:
```bash
$ podcasts update radiolab
$ podcasts update radiolab 99percent
$ podcasts download radiolab
```

# searching
You can search for podcasts by passing the `-s` or `--silent` option to
download like so:
```bash
$ podcasts download -s
```
which will print out a list of matching episodes. In this case, it will
print out a list of the most recent episode from each podcast (the
default).

You can supply search terms with the `-S` or `--search` flag like so:
```bash
$ podcasts download -sS "some text"
```
which will find all the podcasts with 'some text' in either their title
or description.

By default the -S flag does simple shell style globing like:
* * matches any string of characters
* ? matches any single character
* [seq] matches and character in seq
* [!seq] matches any character not in seq

By passing the `-E` or `--regex` flag you can also do searches using
regex matchers:
```bash
$ podcasts download -sES "^1[0-9]{2}.*"
```
would find all of the episodes that start with a number from 100 to 199.
(*Note* the regex matching uses 'search' style matching, so you need a
^ at the front to match from the start of a string. Also the exact syntax
for the regex is python style (complete documentation found [here][1]))

Once you have found what episodes you want to download, all you have to
do is remove the small s flag (`-s`) and re-run the command:
```bash
$ podcasts download -S *
```
will download all podcasts episodes (not recommended).
(*Note*: while in theory this should work, unless you wrap the * in
quotes like this: "*", the shell will try to expand that to 
all of the filenames in the current directory.)

Also the same per-podcast filtering rules as above apply. You can
specify the name of one or more podcasts at the end to make the
search apply to only those podcasts.
```bash
$ podcasts download -sS * radiolab
```
will match all of the podcasts from radiolab.

# full documentation
```bash
$ podcasts --help
usage: podcasts [-h] [-i] {update,download} ...

optional arguments:
  -h, --help            show this help message and exit
  -i, --interactive, --repl
                        run in an interactive loop.

commands:
  {update,download}
    update              update podcast feeds.
    download            download podcast episodes. If run with no search
                        arguments, download the most recent podcast episode.
$ podcasts update --help                        
usage: podcasts update [-h] [podcast [podcast ...]]

positional arguments:
  podcast     Add podcast names to narrow the update.

optional arguments:
  -h, --help  show this help message and exit
$ podcasts download --help
usage: podcasts download [-h] [-S SEARCH] [-E] [-I] [-s] [-f]
                         [podcast [podcast ...]]

positional arguments:
  podcast               apply the actions to a subset of the podcasts.

optional arguments:
  -h, --help            show this help message and exit
  -S SEARCH, --search SEARCH
                        search for a podcast to download.
  -E, --regex           Use a regex instead of the normal shell-like globbing.
  -I, --case-sensitive  Run a case-senseitive search, insead of the default,
                        case-insenseitive search.
  -s, --silent, -d, --dry-run
                        don't actually download any files, just show which
                        file would have been downloaded.
  -f, --force           force downloading the the podcasts, even if they are
                        already on disk.
```

# hacking
While this program is pretty useful in its current state, there are definitly
some things that could use some improvement.
* File storage is not as flexable as it could be
* Could have more versatile searching function (or at least one that
makes more sense)
* Could put in more configuration options, and move configuration files
together.
* Could do a lot of internal code cleanup
* Maybe track read/unread podcasts. I'm still on the fence about this
* Get more info about podcast episodes, and maybe use that to search
as well

The program itself is pretty straightforward, most the searching and
filter tasks are done through simple iterators and filters so
it could be easy to simply add some more. Printing and interface
could use some improvement (like printing search results for example).

Anyways feel free to either contribute or open up issues on github 
with ideas or suggestions.

-- Josh Kunz

  [1]: http://docs.python.org/library/re.html#regular-expression-syntax
