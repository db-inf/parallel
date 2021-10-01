parallel.sh
===========
parallel.sh is a meta-script to run a command in parallel on a possible large set of
arguments/filenames, with concurrency limited by the number of processors. When multiple instances
of parallel.sh itself are started, they execute one after the other, not in parallel.

For instance jpegtran loads a jpeg image in memory completely. When starting a large number of
jpegtran processes at once, this leads to swapping from RAM to disk and back. linux does
not handle this gracefully, because each process is alotted only small slices of execution time,
swapping its working memory in to an out of RAM each time. This script limits the number of
concurrent jpegtran processes to the number of processors.

Usage:

	parallel.sh [ -h | --help ]
	parallel.sh [ [ -e | --expansion ] expansionformat ] command... -- arguments...
	linkingname.sh [ [ -e | --expansion ] expansionformat ] arguments...

	arguments : the given or predefined command is run once for each argument (often a filename)
	command : the executable and it's options that will handle each argument in turn. Use one or more
		"{}" options as placeholders; they will each be replaced by the argument. Each "{}" can be
		prefixed and suffixed by fixed strings such as paths and filename extensions.
	-e expansionformat
	--expansion expansionformat : without this option, the arguments are collected and expanded with
		the bash parameter expansion syntax "${@}"; with this option, the expansionformat is inserted
		before the closing '}'. For example, -e %.jpg would lead to the expansion of "${@%.jpg}", for
		which batch removes the ".jpg" suffix of each of the arguments. BEWARE: the expansion formats
		:offset and :offset:length do NOT expand to a substring of each of the arguments; instead they
		expand only a subarray of the arguments.

	- When invoked under it's own name 'parallel.sh', the arguments up to but not including "--" define
	 the command to execute, wherein one or more occurences of an argument "{}", serving as placeholder,
	 will be replaced in turn by the expansion (as documented above) of each of the arguments after "--".

	- Commands can be predefined in this script, by linking to it from another filename, here called
	 linkingname, for which the command has been defined in the case structure here-under, and
	 invoking the script under that linkingname. Only the filename itself of the link is tested,
	 not the path to it.

Examples:

- recompress a set of mp3 files to another directory :

	`$ parallel.sh ffmpeg {} -c:a libmp3lame -vbr:a 2 /tmp/{} -- *.mp3`

- recompress a set of mp3 files to aac format; note that the expansion option first removes
 the .mp3 suffix, to add it again for the input option, and replacing it with .m4a for the
 output option to ffmpeg :
 
	`$ parallel.sh --expansion %.??? ffmpeg -i {}.mp3 -c:a libfdk_aac -vbr:a 2 {}.m4a -- *.mp3`
