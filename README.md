# elexwork

[JMP](http://jmp.com) scripts and other support files used for preprocessing Open Elections data.

Though JMP is a commercial product, the scripts might still be informative to those without JMP
(BTW, most major universities have site-wide licenses).
The bulk of the processing is:
1. cleaning up the TabulaPDF CSV files using regex to fix observed error patterns
1. reshaping the tables such as stacking columns or computing variables
1. summarizing for validation

For now, at least, the files are specific to the county, so they're within
the county folder.
