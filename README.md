# Ideas

## Nuget mirror/cache

For a large amount of users on the same network, it would be ideal to have a
local nuget server as a cache for the nuget.org server. This would have the
advantages of greatly improved latency and bandwidth, in addition to being able
to retrieve from a cache when nuget is down. The latter is particularly useful
in larger organisations as this can cause delays in development through CI and
builds being unable to retrieve new versions of packages. If we could retrieve
from a cache instead, then this problem is avoided.

This should be simple enough that the existing Nuget server repository can be
modified to include this feature. I'm thinking that the cache workflow should
be something like:

* If a package is provided by the local server, serve as per usual.
* If a package exists on a remote server and we have the version in cache,
  serve the cached version.
* When we cannot contact the remote server, remember this state so that we only
  retry every minute or so (configurable). Keep timeouts low so that the impact
 the first time that we cannot contact the remote server is low.

## Music server

Create a SPA that basically just queries a JSON API. This should be able to
serve any type of music. Now that Chrome and Firefox support FLAC, my music
indexing project from late 2016 can be revived.

A nice player already exists at https://github.com/521dimensions/amplitudejs
that can provide the user interface. Appears to be able to plug into a REST API for easy integration.

## HCL Parser for .NET

Wrap Newtonsoft.Json so that HCL can be read/written to. Not particularly
necessary but would be nice to have for integration with some other software
(e.g. Hashicorp products).

## Web-based subtitles and video player

What I would like is a web page that allows me to search through transcripts or
subtitles of TV shows and/or movies, and in particular highlight when the text
is currently being played. One use case is to be able to search for a quote and
then have the clip play at that point in time.

This could involve integrating with existing APIs to collect subtitles or even
extract them immediately from the video files themselves.

I already have a prototype of this, again from late 2016 which performs this
crudely. It displays the text in the video and highlights it as the video is
playing. This would need to be greatly improved for personal and general use.
In particular it would need full text search, in addition to classification of
movies/TV shows.

## `rr` for .NET

**Update** This is now well supported in Visual Studio as an official feature: https://devblogs.microsoft.com/visualstudio/introducing-time-travel-debugging-for-visual-studio-enterprise-2019/

[rr](http://rr-project.org/) is a debugging application that aids in debugging.
It would be nice if there was something like this in C\#.

As it turns out, you can do this in Visual Studio using a feature called
'Historical Debugging', which is a feature of 'IntelliTrace'.

For more information on the Visual Studio feature:
https://docs.microsoft.com/en-gb/visualstudio/debugger/historical-debugging

Unfortunately the feature is only available for VS Enterprise but does appear to
perform the 'time travel debugging' feature that we'd want.

Any implementation outside of Visual Studio would be a very difficult project,
something like JetBrain's Rider has to do some of the same stuff to implement
debugging so it might be a good reference.

## Editor config generator

In many projects, people use EditorConfig to ensure that regardless of the editor
they use, the resulting editor configuration is consistent. This serves the intended
purpose well, however, I would like to be able to build something that generates
permanent configuration values for a text editor based on information in a
`.editorconfig` file.

I'm thinking something along the lines of a web application where you paste an
`.editorconfig` file, and Vim, Emacs, Visual Studio Code, etc configuration values
are generated. 

Ideally there would be basic instructions on how to apply these configurations.

## Port the Rust docs generator to .NET

I like the look and behaviour of the Rust documentation generator. This would be
nice to have for .NET code. In general this should be as simple as parsing the
XML file generated by the C# compiler into something that can be presented nicely.

## Expiring content provider

*After some thought, this is actually quite simple. The host service just needs to know the expiry time. It should not know the contents of the file. The encryption of the file can be done in JS and uploaded directly to the host. For more information on how to do this: [StackOverflow](https://stackoverflow.com/questions/25593574/progressive-upload-and-encryption-with-cryptojs)*

A file host that is encrypted at rest and content expires within a given period
of time. Similar to Signal's disappearing messages or Allo's incognito chat in
that you could have the content disappear after 1 hour (for example).

Content must also be delivered over HTTPS to ensure that intermediaries cannot
alter or observe the content.

Something like [file.io](https://file.io/) but open source and self-hostable.

Basic ideas that I've been thinking of on how to implement this:

* Use Salsa20/XSalsa20/ChaCha20 for encryption. Have the client generate a key.
* Ensure that the content has a MAC, something like BLAKE2/SHA-3 should be OK.
* Have the option of providing a public key to encrypt the key with. Makes transmission of the key safe and for the recipient-only.
* JS should be used for encryption and decryption, so that the server really has no knowledge of anything besides the size of the blocks of bytes given to it.
* Perhaps write a header for metadata in the encryption process. The reason why this could be useful is for retaining the filename. For example, if I store a file named `Big Company X - Final Contract.docx` in the encrypted store, I might want to retain this information so that when I download later, I also get to download with the same filename.
* Progress for upload and encryption is important. Because this is done in JS, this might be slow, especially for large files.
* Try to keep browser memory usage low to ensure mobile browsers can work.
* Mega (i.e. mega.nz) appears to have the idea right with the UI -- try to emulate some of this.

## Port Chalk to .Net

**This is now available on Nuget: [SJP.Fabulous](https://www.nuget.org/packages/SJP.Fabulous/)**

~~[Chalk](https://github.com/chalk/chalk) is a nice API for displaying coloured
text in a console. This should be straightforward to port. Ideally we would
build using a default object and then chain via an immutable object with fluent
methods that build a new object.~~

## An NTFS change journal provider for .NET

**A nuget package has now been published. See the project repo at [FsNotify](https://github.com/sjp/FsNotify) for more info.**

**Additionally, it maybe a good idea to wrap the code in [Everything](https://www.voidtools.com/support/everything/sdk/csharp/) using PInvoke. That may avoid some of the problems of `FileSystemWatcher`.**

*After some work it seems that the use of change journals, while promising, relies upon permissions that hinder its usefulness. If the application has Administrator or System privileges then working with the change journal is going to work fine. It may still be useful to extend beyond FileSystemWatcher. The primary reason is for remote files. See the following links for more information (particularly "FileSystemWatcher Fencing (Part 1)"): [FileSystemWatcher Follies](https://blogs.msdn.microsoft.com/winsdk/2015/05/19/filesystemwatcher-follies/), [FileSystemWatcher Fencing (Part 1)](https://blogs.msdn.microsoft.com/winsdk/2015/06/04/filesystemwatcher-fencingpart-1/), [FileSystemWatcher Fencing (Part 2)](https://blogs.msdn.microsoft.com/winsdk/2015/09/15/filesystemwatcher-fencingpart-2/). Additionally, for a pretty good implementation of Change Journal reading, see: [Scrutiny](https://github.com/beaugunderson/scrutiny)*

There exists a `FileSystemWatcher` for C\# but it has some limitations which mean it does not necessarily capture all file system events. A workaround is to use polling in addition to capturing events via `FileSystemWatcher`, but this is not particularly appealing or performant.

Using the NTFS change journal is a more complete solution. See https://blogs.msdn.microsoft.com/oldnewthing/20110812-00/?p=9913/ for more information on this.

An ideal solution would attempt to use the NTFS change journal when possible, roll back to the `FileSystemWatcher` otherwise, and use polling as a last resort. An abstraction layer on top to automate this would be nice. Note: there are existing Nuget packages which do perform some of this already, but are missing the change journal handling.

For further evidence that reading NTFS change journal records is performant, it is the same technique used in the very good search tool [Everything](https://voidtools.com)

## Repackage the LockCheck program as a library

**This is now available as a Nuget packge, see [Sherlock](https://github.com/sjp/Sherlock)**

The [LockCheck](https://github.com/cklutz/LockCheck) program uses a good and reliable method for determining whether a file is currently in use and locked by another process. It is a good program, but this would be more useful as a library so that other applications can use it.

This is currently possible to be referenced as a library but it is not ideal if there is an entry point present. Additionally packaging as a .NET Standard application would give the library broader appeal.

## A .NET package for wrapping EnumDisplaySettingsEx

What might be useful is an easy way of determining the refresh rate of monitors on Windows. The primary reason this would be useful is if you wanted to display something often, but throttle to a maximum of the display rate of the monitor as the user would not be able to see it otherwise anyway.

See the [DEVMODE](https://msdn.microsoft.com/en-us/library/windows/desktop/dd183565(v=vs.85).aspx) `dmDisplayFrequency` field for getting the value of the monitor's refresh rate.

## An in-memory full-text search index

.NET currently has a good full text search library in the form of [Lucene.Net](https://lucenenet.apache.org/). What might be a good idea is porting [Whoosh](https://whoosh.readthedocs.io/en/latest/) from Python to C#. An alternative is [bleve](https://github.com/blevesearch/bleve) from Go.

Alternatively we could wrap [Lucene.Net](https://lucenenet.apache.org/) so that it is easier or nicer to use than the port that is almost directly the same as Java code.

The intended use cases are to be able to search across databases agnostically, like Elasticsearch, but also being able to run the search engine in-process with other code.

The main purpose for this is for me to be able to query against a database of audio tags. I want to be able to search against song titles, artists, albums, etc when I make a query and have them be ranked appropriately. Using the database to do this is OK (i.e. can have multiple 'like' operators), but a full text search index is definitely more suited to the job.

Further reading:

* There was attention on GitHub and HN for a JS search engine called **Wade**. A lot of discussion about the implementation was provided in a blog post and on HN. Further info:
	* https://github.com/kbrsh/wade
	* https://blog.kabir.ml/posts/inside-wade.html
	* https://news.ycombinator.com/item?id=14805395

## A Unicode/Emoji library

Wrap the unicode spec as a .NET package. Do this by converting the XML data into a SQLite database. Gives us information on each of the characters and makes them searchable.

## A disk cache library

**A nuget package has been published. Project repo is available at [DiskCache](https://github.com/sjp/DiskCache).**

Want to be able to transcode and have a cache to store the results. The resulting objects should have various cache policies, LRU, FIFO, LIFO, manual cache expiration. Additionally this should store a configurable maximum amount of temporary storage. Should work similar to MemoryCache but DiskCache instead. There may already be a library that does this, but I have not yet done my research.

## A database streaming notification library

With several (but not all) vendors, it is possible to receive events from databases. One example of this is SQL Server's "event notifications". These behave similar to remote triggers.

It may be useful to have an application that subscribes to database events. One situation is when performing a software upgrade, and tracking any changes to the database that the application would need to know about when upgrading has completed.

## Generic SQL parser

What would be useful is to have a parser for SQL queries. The aim of such a query would be to ensure that any hardcoded query strings will be correct over different databases.

This would not only be useful in porting between MSSQL, Oracle, PG, SQLite, etc but also between different versions of databases. For example, if you need to support SQL Server 2008 R2+, then a query cannot rely upon syntax that is only present in newer versions. A parser would help in this situation to ensure that the query is safe to use for all supported versions and platforms.

## Regular expression data generator

**A package has been created to do this: [GenerationRex](https://github.com/sjp/GenerationRex)**

There is an existing tool, Rex, which generates data from a regular expression for .NET. What would be useful is updating this to work with .NET Standard, additionally, because there is no source available this will have to be decompiled from the initial release.
