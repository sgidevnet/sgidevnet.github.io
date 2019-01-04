# Quirks noted when crosscompiling software

##### Bash

The linker in binutils 2.17a doesn't understand -rdynamic. In the source directory for bash, run the following to correct for this:
`find . -type f | xargs sed -i 's/-rdynamic/-export-dynamic/g'`
