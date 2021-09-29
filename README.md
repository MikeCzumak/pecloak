# pecloak
DEPRECATED - A Multi-Pass Encoder &amp; Heuristic Sandbox Bypass AV Evasion Tool

### Deprecation Notice
This repository is deprecated and I am no longer actively maintaining it. Keeping it here for those interested in code. 

## Overview

`pecloak.py` is a tool I developed while doing research on anti-virus evasion. It was designed to use simple xor, add, or sub instructions to encode Windows executable files in order to defeat sandbox-based, heuristic run time detections and minimize the static nature of the decoding/heuristic code to combat signature detection. At the time of its development it was relatively successfuly at evading detection from most major AV products (though that may not be the case today). It was referenced in the [Antivirus Hacker's Handbook](https://www.amazon.com/Antivirus-Hackers-Handbook-Joxean-Koret/dp/1119028752) and [The Hacker Playbook 2: Practical Guide To Penetration Testing](https://www.amazon.com/Hacker-Playbook-Practical-Penetration-Testing/dp/1512214566). 

I wrote a pretty detailed post about my AV research and the development of pecloak here: https://mikeczumak.com/blog/pecloak-py-an-experiment-in-av-evasion/. 

I am not actively maintaining this but feel free to leverage the code if you feel it can be useful in your research. 

## License 

LICENSE/WARRANTY: This program is free software: you can redistribute it and/or modify it under the terms of the GNU General Public License as published by the Free Software Foundation, either version 3 of the License, or(at your option) any later version.

This program is distributed in the hope that it will be useful, but WITHOUT ANY WARRANTY; without even the implied warranty of MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE. See the GNU General Public License for more details.

## Dependencies 
- [pydasm](http://sourceforge.net/projects/winappdbg/files/additional%20packages/PyDasm/)
- [pefile](https://code.google.com/p/pefile/downloads/list)
- [SectionDoubleP](http://git.n0p.cc/?p=SectionDoubleP.git;a=summary)

## Usage
### Help Output
```
=========================================================================
|                         peCloak.py (beta)                             |
|  A Multi-Pass Encoder & Heuristic Sandbox Bypass AV Evasion Tool      |
|                                                                       |
|           Author: Mike Czumak | T_V3rn1x | @SecuritySift              |
|    Usage: peCloak.py [options] [path_to_pe_file] (-h or --help)       |
=========================================================================



Usage: peCloak.py [[options]] [path_to_pe_file]

To encode a file (w/ 3 heuristic bypass iterations) just call the script with a target filename and no options.
For abbreviated help, use the -h option


========= INFORMATIONAL OPTIONS =========

-h, --help                                You're looking at it

-i, --info= [section]                     Print info for image / section
                                             - Possible options:
                                               all = print image info and detailed info for all sections
                                               none = print info for image only
                                               [section_name] = print image info and detailed info for named section

-d, --dump [section:start:stop]           Dump (print) hex / ascii for range of bytes of named section
                                             - The range is an offset (from section start) and byte count
                                             - You can provide the values in either decimal or hex but
                                               keep in mind that these are offsets from the section start!

                                             - Example 1: -p .text:0-2000 means start at offset 0 of
                                               .text section and print 2000 bytes in total

                                             - Example2 : -p .text:14h-1000 means start at offset 14 hex (20d) of
                                               .text section and print 1000 bytes in total


========= CORE CLOAK OPTIONS =========

-e, --encode= [section:offset:length]     Encode the named section. By default, running this script without
                                          specifying this option will encode the .text section. You can also
                                          use this option to specify a different section. To name multiple sections
                                          separate them by commas. Note: range values are base 10 integers!
                                             - Examples:
                                               -e .text:5:500         encode 500 bytes of .text section starting at offset 5
                                               -e .rdata:100          encode .rdata section starting at offset 100
                                               -e .text, .rdata       encode all of .text and .rdata sections
                                               -e .text, rdata:0:100  encode all of .text section and first 100 bytes of .rdata

-a, --add                                 Force addition of new section (.NewSec) for code cave
                                          Otherwise the script will try to add it to the existing .text/.code section
                                          if enough room is found.
-H, --heuristic= [x]                      Specify the number of iterations for the
                                          heuristic bypass code (default=3)

-p, --preset= [preset]                    Use a preset encoding configuration to cloak your executable
                                          Useful if you cloak the same types of files frequenty (e.g. Metasploit payloads)
                                          Use a ? as the option value to see a list of currently configured presets

========= ADDITIONAL MODIFICATION OPTIONS =========

-s, --section= [section name]             Specify the pe section to modify
                                          that will be modified (default is none)

                                             - Possible options include any valid sections other than .text:
                                               .rsrc, .rdata, .data, etc.

                                          Important: If you choose a section to modify that is also being encoded
                                          the modification happens first!

                                          the modification happens first!

-m, --modification= [x]                   Specify the modification type to make for the target
                                          section (other than .text)
                                             - Possible options:
                                               1 = swap case of letters (hex values x41-x59 and x61-x7a)
                                               2 = replace letters with zeros (x00)
                                               3 = replace non-letters with zeros (x00)

-r, --range= [x:y]                        Specify the range (in bytes) for modification within the
                                          target pe section (other than .text)

                                             - Format = start:end Example: --range=0:2000
                                             - The default is to start at the beginning and
                                               modify the entire section
                                             - Specifying 0 for the start value will also
                                               start at the beginning
                                             - Specifying a number larger than the size of
                                               the section will default to the end of the section


========= OTHER OPTIONS =========

-c, --chunk= [chunk_size]                 Split the file into chunks of designated size for AV scanning
                                          May be useful if you need to determine which portion of a file
                                          is triggering detection
```
