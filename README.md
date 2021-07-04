Script to sync watched status between media centers, supports Kodi and Plex for now.
Both Episodes and Movies are supported. Sync at Tv Show/season level is not supported.
Basic knowledge of python is expected to configure/run this.

This is a fork of hoodakaushal's original that simplifies configuration and adds ability to sync between Linux to Windows installations, as well as setups where the file path is different.

Matching files across media centers is done by file path. A video in plex and kodi is considered same if they both have the same underlying file.
This has the advantage of not requiring any external API (eg TVDB) and can be run offline.
Paths are treated as case sensitive by default, on *nix systems this is fine, on Windows it can cause videos to not be matched.
This also means that scenarios where one file contains multiple episodes are not supported - either they'll all end up
being marked watched or unwatched, depending on the order in which they are processed.
If your media tagging in plex and kodi is not the same this can lead to incorrect behavior.
I tested this on my setup (which only used local files), so no guarantees for remote shares (eg samba, nfs, etc) - it
depends on both media centers representing the path of the video in same fashion.

Dependencies:
python3
plexapi and requests module for python (pip install -r requirements.txt)

Usage/Config:
Change the configuration for plex and kodi according to your setup in sync.py and execute it as a python module
(refer to run.sh/run.bat for sample command)

Set the sync mode and strictness as you want (default is bidirectional sync without strict checking)

    Sync mode: (syncDir Variable)
    0 -> UNIDIRECTIONAL FROM a to b, a always overrides b. In strict mode media in b but not a is ignored.
    1 -> BIDIRECTIONAL, if a and b conflict, mark both as watched
    2 -> BIDIRECTIONAL, if a and b conflict, mark both as unwatched

    strict sync:
    True -> If media in a and not b, raise error. If media in b and not a, raise error only for BIDIRECTIONAL sync mode.
    False -> Ignore discrepancies in media in a and b.
    Note that if strict, checking is done before doing any updates.

    Normalization:
    Set normalization['enable'] to True to normalize filepaths between installations. Eg. Modify drive letter from Z: to M: to the path matches between installs.
    The pathMap entries below can be edited or removed as needed or additional lines can be added. The first reference in the list will be updated to the second instance when the matching is completed to allow syncing between different paths.
    
    Log Changes:
    Set logChanges to True to output details of failed updated to console and in the log. False will disable logging output of changes.

    a and b are the first and second argument to the MediaSyncer constructor (the second to last line in kodiplex/sync.py)
 
    kodiURL:
    Update this with the URL of your Kodi Install.
    
    plexURL:
    Update this with the URL of your Plex Install.
    
    plexToken:
    Add your Plex token here for authentication. Otherwise this can be removed by setting this variable to None (no quotes)
 
By default the script will sync from Kodi to Plex

    sync = MediaSyncer(kodiMedia, plexMedia, syncDir, strict, logChanges, normalize)

 And for syncing only from plex to kodi,

    sync = MediaSyncer(kodiMedia, plexMedia, syncDir, strict, logChanges, normalize)

And just let it run!

You could also modify with a small amount of Python knowledge to sync Plex to Plex or Kodi to Kodi if ever needed for any reason.

Once you have it configured to work with your setup, you can have it run periodically via Scheduled Task/cron.

Note on Auth:
By default the script expects to be able to connect to Kodi without auth, and for Plex an auth token is supported.
For plex if you want to use username/password instead, you'll need to change the connect code. Internally plexapi
is used to connect to plex, refer their docs for more connection methods - https://pypi.org/project/PlexAPI/

I don't intend to actively maintain it, but feel free to hit me up if you have a request/suggestion.
