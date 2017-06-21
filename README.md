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
