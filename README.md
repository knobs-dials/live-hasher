# live-hasher

SHA1 hashfile maker for directories.

What it adds is that it can keep watching the directories (for some time), and rehash 
- new files 
- altered files (detected by changes in size and/or mtime) 

This was made for dealing sensibly with files being copied in during data collection, also considering that some files may be appended to after we first see them.

Tries to avoid losing work with an ill-placed Ctrl-C: We write to disk every-so-many files (default 500) and every-so-many bytes bytes (default 1GB), and a new hashfile is saved to a temporary file, then moved into place.



### Limitations
                                                                                                               
The main caveat is that when you stop and re-run, it can't really do a size or mtime check,
because the hashfile (our only information store between runs) doesn't contain that information.

There's a "re-hash files younger than X on disk" argument (default 5min, and based on mtime) to help there, 
but doing so makes the assumption that older files never change, and that mtime means local age (e.g. not necessarily true in rsync).
so you should check and be sure that makes sense for your use.


### Arguments:

```
Specify one of -w, -a, or -c, and a directory to read files from:

  live-hasher <-a|-w|-c> dirname/


Usage: live-hasher [options]

Options:
  -h, --help            show this help message and exit
  -a, --add-once        One-shot add of files exist on disk but not in the
                        hash file
  -w, --watch           Add, then keep watching for new/changed files  (by
                        mtime or size changes)
  -c, --check           Check existing entries against disk contents (like
                        md5sum -c / sha1sum -c), relative to the given dir
  -o HASHFILE, --output-filepath=HASHFILE
                        Write hashfile to this specific filename, rather than
                        to hashes.sha1 placed in the directory it's reading
                        from.
  -i INTERVAL, --watch-interval=INTERVAL
                        when using -w: the sleep time between rounds. Defaults
                        to 1 minute.
  -e TIMESPAN, --watch-timespan=TIMESPAN
                        when using -w: time after the last observed to quit.
                        Accepts timespecs like accepts 10min and 1h30s.
                        Defaults to 6 hours.
  -r REDO_RECENCY, --redo-recent=REDO_RECENCY
                        When using -w (first round only), or -a: Files younger
                        than this timespec are rehashed. Safer when you break
                        and re-run one of these watching processes. Defaults
                        to 5min.
  -C SAVE_EVERY_N, --save-interval=SAVE_EVERY_N
                        Save every so-many files hashed (default:500)
  -B SAVE_EVERY_BYTES, --save-interval-bytes=SAVE_EVERY_BYTES
                        Save every so-many megabytes hashed (default: 1GB)
  -v, --verbose         print more debug
```


### Notes:
* One thread per directory argument, which can help speed when they are on different mounts.

* checker code is basically just equivalent to md5sum -c / sha1sum -c

* prints/stores relative paths  (internally it's absolute)

* First-ish version, not thoroughly tested, don't rely on it for your dog's / company's safety
