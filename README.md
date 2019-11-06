# live-hasher

Hashfile checker, hashfile maker.

The addition is a mode that keeps watching the given directory (listdirs with 1-minute interval),
and rehashes files that change mtime and/or size.
The point is dealing sensibly with datasets while being collected, files while being copied in, and such.

Stops after the directory it watches has been idle for some time (defauly 6 hours)

                                                                                                               
The caveat is that when you stop and re-run, it can't really do a size or mtime check,
because the hashfile, as as our primary information, doesn't contain these.

There's a "re-hash files with mtime younger than X" argument to help there, 
but it makes the assumption that older files do not change so be sure that makes sense for you.


Notes:
- code can read/check MD5 or SHA1. Writing is currently only SHA1

- checker code is basically just equivalent to md5sum -c / sha1sum -c

- tries to avoid losing work with an ill-placed Ctrl-C:
-- new hashfile is saved to a temporary file, them moved (makes losing the hash file due to interruption less likely)
-- hashfile is written every-so-many files (default 500) and every-so-many bytes bytes (default 1GB)

- prints/stores relative paths  (internally it's absolute)

