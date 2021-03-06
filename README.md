# Get metadata of files with Vim, 7zip, and CMD

I don't know if this has been done before; I feel like the answer is "no". I looked up "Get metadata with Vim, 7zip, and CMD" on Google and found nothing. Why does 7zip not support MD5?

(Click the "Raw" button on this web page to enable word wrapping.)

````
Step 0. Know that you can paste Vim command mode commands in with : > Edit > Paste

Step 1. Get a list of your files.

Open Vim then add the following text. Only add the text between "--->" and "<---" which you have customized to your files.

start of "file" ("1,1")--->wmic datafile where Name="C:\\path\\to\\the\\files\\filenameU46HOJDGB2.ext" get CreationDate,EightDotThreeFileName,Extension,FileName,FileSize,FileType,LastAccessed,LastModified

"C:\Program Files\7-Zip\7z" h -scrcsha256 filename7CIVJTLRWS.ext
<---end of "file" ("4,0")

where "U46HOJDGB2" and "7CIVJTLRWS" is the number part of your filenames. All filenames are in the format of "TheSameText#", where "TheSameText" is any string which is the entirety of each filename if you remove the numbering part of it; the numbering part is "#", which means any positive integer from 1 to 281474980000000.

Then in command mode run
:$ | 1,4t.O

In normal mode run
#@@

where "#" is the number of files you want to get metadata on minus 2. So if you have 7 files you want to get metadata on it would be
5@@

and the output would be

start of "file"--->wmic datafile where Name="C:\\path\\to\\the\\files\\filenameU46HOJDGB2.ext" get CreationDate,EightDotThreeFileName,Extension,FileName,FileSize,FileType,LastAccessed,LastModified

"C:\Program Files\7-Zip\7z" h -scrcsha256 filename7CIVJTLRWS.ext

wmic datafile where Name="C:\\path\\to\\the\\files\\filenameU46HOJDGB2.ext" get CreationDate,EightDotThreeFileName,Extension,FileName,FileSize,FileType,LastAccessed,LastModified

"C:\Program Files\7-Zip\7z" h -scrcsha256 filename7CIVJTLRWS.ext

wmic datafile where Name="C:\\path\\to\\the\\files\\filenameU46HOJDGB2.ext" get CreationDate,EightDotThreeFileName,Extension,FileName,FileSize,FileType,LastAccessed,LastModified

"C:\Program Files\7-Zip\7z" h -scrcsha256 filename7CIVJTLRWS.ext

wmic datafile where Name="C:\\path\\to\\the\\files\\filenameU46HOJDGB2.ext" get CreationDate,EightDotThreeFileName,Extension,FileName,FileSize,FileType,LastAccessed,LastModified

"C:\Program Files\7-Zip\7z" h -scrcsha256 filename7CIVJTLRWS.ext

wmic datafile where Name="C:\\path\\to\\the\\files\\filenameU46HOJDGB2.ext" get CreationDate,EightDotThreeFileName,Extension,FileName,FileSize,FileType,LastAccessed,LastModified

"C:\Program Files\7-Zip\7z" h -scrcsha256 filename7CIVJTLRWS.ext
<---end of "file"

Then run
:let i=1 | g/U46HOJDGB2/s//\=i/ | let i=i+1
:let j=1 | g/7CIVJTLRWS/s//\=j/ | let j=j+1

Step 2. Get the unformated metadata (see Appendix A)

Open CMD

Go to the directory with the files that you want to get meta data on, run "cd C:\path\to\the\files"

Run "cls"

Copy and paste all of those Step 1 commands into CMD to run them.

Press Ctrl+A

Paste the unformatted metadata into Vim then run the command in Step 3.

Step 3. (referenced above) Format metadata

:%s/\(CreationDate\)\s\+\(EightDotThreeFileName\)\s\+\(Extension\)\s\+\(FileName\)\s\+\(FileSize\)\s\+\(FileType\)\s\+\(LastAccessed\)\s\+\(LastModified\)\s*/\1\r\2\r\3\r\4\r\5\r\6\r\7\r\8/g | %s/CreationDate\n\(.*\n.*\n.*\n.*\n.*\n.*\n.*\n\)\(\S\+ \S\+\|\S\+\)/CreationDate: \2\r\1/g | %s/EightDotThreeFileName\n\(.*\n.*\n.*\n.*\n.*\n.*\n\)\s\{2,}.\+\\\(\S\+ \S\+\|\S\+\)/EightDotThreeFileName: \2\r\1/g | %s/Extension\n\(.*\n.*\n.*\n.*\n.*\n\)\s\{2,}\(\S\+ \S\+\|\S\+\)/Extension: \2\r\1/g | %s/FileName\n\(.*\n.*\n.*\n.*\n\)\s\{2,}\(\S\+ \S\+\|\S\+\)/FileName: \2\r\1/g | %s/FileSize\n\(.*\n.*\n.*\n\)\s\{2,}\(\S\+ \S\+\|\S\+\)/FileSize: \2\r\1/g | %s/FileType\n\(.*\n.*\n\)\s\{2,}\(\S\+ \S\+\|\S\+\)/FileType: \2\r\1/g | %s/LastAccessed\n\(.*\n\)\s\{2,}\(\S\+ \S\+\|\S\+\)/LastAccessed: \2\r\1/g | %s/LastModified\n\s\{2,}\(\S\+ \S\+\|\S\+\)/LastModified: \1/g | %s/\(.*\n\)\{2}.*>.* h -scrcsha256\(.*\n\)\{15}//g | %s/.*>wmic datafile where Name=.*\\/Filename: /g | %s/" get CreationDate,E.*//g | %s/\nEverything is Ok\n\n.*>$//g | %s/\n\nEverything is Ok$//g

(See Appendix B for some info on this command.)

^
works with output of
v

Appendix A. (referenced above) Unformatted metadata example

C:\path\to\the\files>wmic datafile where Name="C:\\path\\to\\the\\files\\filename#.ext" get CreationDate,EightDotThreeFileName,Extension,FileName,FileSize,FileType,LastAccessed,LastModified
CreationDate               EightDotThreeFileName               Extension  FileName   FileSize  FileType              LastAccessed               LastModified
YYYYMMDDHHMMSS.######+***  c:\path\to\the\files\filename#.ext  ext        filename#  ####      [letters or 1 space]  YYYYMMDDHHMMSS.######+***  YYYYMMDDHHMMSS.######


C:\path\to\the\files>"C:\Program Files\7-Zip\7z" h -scrcsha256 filename#.ext

7-Zip 18.05 (x64) : Copyright (c) 1999-2018 Igor Pavlov : 2018-04-30

Scanning
1 file, ###### bytes (### [letters])

SHA256                                                                    Size  Name
---------------------------------------------------------------- -------------  ------------
0123456789ABCDEF0123456789ABCDEF0123456789ABCDEF0123456789ABCDEF        ######  filename#.ext
---------------------------------------------------------------- -------------  ------------
0123456789ABCDEF0123456789ABCDEF0123456789ABCDEF0123456789ABCDEF        ######

Size: ######

SHA256 for data:              0123456789ABCDEF0123456789ABCDEF0123456789ABCDEF0123456789ABCDEF

Everything is Ok

C:\path\to\the\files>
C:\path\to\the\files>wmic datafile where Name="C:\\path\\to\\the\\files\\filename#.ext" get CreationDate,EightDotThreeFileName,Extension,FileName,FileSize,FileType,LastAccessed,LastModified
CreationDate               EightDotThreeFileName               Extension  FileName   FileSize  FileType              LastAccessed               LastModified
YYYYMMDDHHMMSS.######+***  c:\path\to\the\files\filename#.ext  ext        filename#  ####      [letters or 1 space]  YYYYMMDDHHMMSS.######+***  YYYYMMDDHHMMSS.######


C:\path\to\the\files>"C:\Program Files\7-Zip\7z" h -scrcsha256 filename#.ext

7-Zip 18.05 (x64) : Copyright (c) 1999-2018 Igor Pavlov : 2018-04-30

Scanning
1 file, ###### bytes (### [letters])

SHA256                                                                    Size  Name
---------------------------------------------------------------- -------------  ------------
0123456789ABCDEF0123456789ABCDEF0123456789ABCDEF0123456789ABCDEF        ######  filename#.ext
---------------------------------------------------------------- -------------  ------------
0123456789ABCDEF0123456789ABCDEF0123456789ABCDEF0123456789ABCDEF        ######

Size: ######

SHA256 for data:              0123456789ABCDEF0123456789ABCDEF0123456789ABCDEF0123456789ABCDEF

Everything is Ok

C:\path\to\the\files>
C:\path\to\the\files>wmic datafile where Name="C:\\path\\to\\the\\files\\filename#.ext" get CreationDate,EightDotThreeFileName,Extension,FileName,FileSize,FileType,LastAccessed,LastModified
CreationDate               EightDotThreeFileName               Extension  FileName   FileSize  FileType              LastAccessed               LastModified
YYYYMMDDHHMMSS.######+***  c:\path\to\the\files\filename#.ext  ext        filename#  ####      [letters or 1 space]  YYYYMMDDHHMMSS.######+***  YYYYMMDDHHMMSS.######


C:\path\to\the\files>"C:\Program Files\7-Zip\7z" h -scrcsha256 filename#.ext

7-Zip 18.05 (x64) : Copyright (c) 1999-2018 Igor Pavlov : 2018-04-30

Scanning
1 file, ###### bytes (### [letters])

SHA256                                                                    Size  Name
---------------------------------------------------------------- -------------  ------------
0123456789ABCDEF0123456789ABCDEF0123456789ABCDEF0123456789ABCDEF        ######  filename#.ext
---------------------------------------------------------------- -------------  ------------
0123456789ABCDEF0123456789ABCDEF0123456789ABCDEF0123456789ABCDEF        ######

Size: ######

SHA256 for data:              0123456789ABCDEF0123456789ABCDEF0123456789ABCDEF0123456789ABCDEF

Everything is Ok

Appendix B. (referenced above) How the Vim command works

(Not needed:
:%s/.:\\.*7z" h -scrcsha256 \(.*\n\)\{15}//g
That was used for something else.)

Simple model:

unique1                    unique2                                   unique3    unique4   unique5   unique6    unique7                    unique8
unique9                    unique10                                  unique11   unique12  unique13  unique14   unique15                   unique16

:%s/\(unique1\)\s\+\(unique2\)\s\+\(unique3\)\s\+\(unique4\)\s\+\(unique5\)\s\+\(unique6\)\s\+\(unique7\)\s\+\(unique8\)\s*/\1\r\2\r\3\r\4\r\5\r\6\r\7\r\8/g

unique1
unique2
unique3
unique4
unique5
unique6
unique7
unique8
unique9                    unique10                                  unique11   unique12  unique13  unique14   unique15                   unique16

:%s/\n\(.*\n.*\n.*\n.*\n.*\n.*\n.*\n\)\(unique9\)/\2\r\1/g | %s/\n\(.*\n.*\n.*\n.*\n.*\n.*\n\).*\(unique10\)/\2\r\1/g | %s/\n\(.*\n.*\n.*\n.*\n.*\n\).*\(unique11\)/\2\r\1/g | %s/\n\(.*\n.*\n.*\n.*\n\).*\(unique12\)/\2\r\1/g | %s/\n\(.*\n.*\n.*\n\).*\(unique13\)/\2\r\1/g | %s/\n\(.*\n.*\n\).*\(unique14\)/\2\r\1/g | %s/\n\(.*\n\).*\(unique15\)/\2\r\1/g | %s/\n.*\(unique16\)/\1/g

unique1 unique9
unique2 unique10
unique3 unique11
unique4 unique12
unique5 unique13
unique6 unique14
unique7 unique15
unique8 unique16

Which correlates to the following:

CreationDate               EightDotThreeFileName               Extension  FileName   FileSize  FileType              LastAccessed               LastModified
YYYYMMDDHHMMSS.######+***  c:\path\to\the\files\filename#.ext  ext        filename#  ####      [letters or 1 space]  YYYYMMDDHHMMSS.######+***  YYYYMMDDHHMMSS.######

:%s/\(CreationDate\)\s\+\(EightDotThreeFileName\)\s\+\(Extension\)\s\+\(FileName\)\s\+\(FileSize\)\s\+\(FileType\)\s\+\(LastAccessed\)\s\+\(LastModified\)\s*/\1\r\2\r\3\r\4\r\5\r\6\r\7\r\8/g

CreationDate
EightDotThreeFileName
Extension
FileName
FileSize
FileType
LastAccessed
LastModified
YYYYMMDDHHMMSS.######+***  c:\path\to\the\files\filename#.ext  ext        filename#  ####      [letters or 1 space]  YYYYMMDDHHMMSS.######+***  YYYYMMDDHHMMSS.######

:%s/\n\(.*\n.*\n.*\n.*\n.*\n.*\n.*\n\)\(\S\+ \S\+\|\S\+\)/: \2\r\1/g | %s/\n\(.*\n.*\n.*\n.*\n.*\n.*\n\)\s\{2,}.\+\\\(\S\+ \S\+\|\S\+\)/: \2\r\1/g | %s/\n\(.*\n.*\n.*\n.*\n.*\n\)\s\{2,}\(\S\+ \S\+\|\S\+\)/: \2\r\1/g | %s/\n\(.*\n.*\n.*\n.*\n\)\s\{2,}\(\S\+ \S\+\|\S\+\)/: \2\r\1/g | %s/\n\(.*\n.*\n.*\n\)\s\{2,}\(\S\+ \S\+\|\S\+\)/: \2\r\1/g | %s/\n\(.*\n.*\n\)\s\{2,}\(\S\+ \S\+\|\S\+\)/: \2\r\1/g | %s/\n\(.*\n\)\s\{2,}\(\S\+ \S\+\|\S\+\)/: \2\r\1/g | %s/\n\s\{2,}\(\S\+ \S\+\|\S\+\)/: \1/g

CreationDate: YYYYMMDDHHMMSS.######+***
EightDotThreeFileName: filename#.ext
Extension: ext
FileName: filename#
FileSize: ####
FileType: [letters or 1 space character]
LastAccessed: YYYYMMDDHHMMSS.######+***
LastModified: YYYYMMDDHHMMSS.######

^
similar
v

CreationDate               EightDotThreeFileName               Extension  FileName   FileSize  FileType              LastAccessed               LastModified
YYYYMMDDHHMMSS.######+***  c:\path\to\the\files\filename#.ext  ext        filename#  ####      [letters or 1 space]  YYYYMMDDHHMMSS.######+***  YYYYMMDDHHMMSS.######

:%s/\(CreationDate\)\s\+\(EightDotThreeFileName\)\s\+\(Extension\)\s\+\(FileName\)\s\+\(FileSize\)\s\+\(FileType\)\s\+\(LastAccessed\)\s\+\(LastModified\)\s*/\1\r\2\r\3\r\4\r\5\r\6\r\7\r\8/g | %s/CreationDate\n\(.*\n.*\n.*\n.*\n.*\n.*\n.*\n\)\(\S\+ \S\+\|\S\+\)/CreationDate: \2\r\1/g | %s/EightDotThreeFileName\n\(.*\n.*\n.*\n.*\n.*\n.*\n\)\s\{2,}.\+\\\(\S\+ \S\+\|\S\+\)/EightDotThreeFileName: \2\r\1/g | %s/Extension\n\(.*\n.*\n.*\n.*\n.*\n\)\s\{2,}\(\S\+ \S\+\|\S\+\)/Extension: \2\r\1/g | %s/FileName\n\(.*\n.*\n.*\n.*\n\)\s\{2,}\(\S\+ \S\+\|\S\+\)/FileName: \2\r\1/g | %s/FileSize\n\(.*\n.*\n.*\n\)\s\{2,}\(\S\+ \S\+\|\S\+\)/FileSize: \2\r\1/g | %s/FileType\n\(.*\n.*\n\)\s\{2,}\(\S\+ \S\+\|\S\+\)/FileType: \2\r\1/g | %s/LastAccessed\n\(.*\n\)\s\{2,}\(\S\+ \S\+\|\S\+\)/LastAccessed: \2\r\1/g | %s/LastModified\n\s\{2,}\(\S\+ \S\+\|\S\+\)/LastModified: \1/g

CreationDate: YYYYMMDDHHMMSS.######+***
EightDotThreeFileName: filename#.ext
Extension: ext
FileName: filename#
FileSize: ####
FileType: [letters or 1 space character]
LastAccessed: YYYYMMDDHHMMSS.######+***
LastModified: YYYYMMDDHHMMSS.######

delete other crap
v

:%s/\(.*\n\)\{2}.*>.* h -scrcsha256\(.*\n\)\{15}//g
:%s/.*>wmic datafile where Name=.*\\/Filename: /g
:%s/" get CreationDate,E.*//g
:%s/\nEverything is Ok\n\n.*>$//g
:%s/\n\nEverything is Ok$//g

^
combined
v

See Step 3 above for the combined command.
````
