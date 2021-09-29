parallel.sh is a meta-script to run a command in parallel on a possible large set of
arguments/filenames, with concurrency limited by the number of processors.

When invoked under it's own name 'parallel.sh', the arguments up to but not including "--" define
the command to execute, wherein one or more occurences of an argument "{}", serving as placeholder,
will be replaced in turn by each of the arguments after "--" (typically a list of filenames for the
command to operate on in parallel).

Commands can be predefined in this script, by linking to it from another filename, for which the command
has been defined in the case structure here-under, and invoking the script under that name. Only the filename
itself is tested, not the path to it.

Example:

    $ parallel.sh ffmpeg {} -c:a libmp3lame -vbr:a 2 /tmp/{} -- *.mp3
