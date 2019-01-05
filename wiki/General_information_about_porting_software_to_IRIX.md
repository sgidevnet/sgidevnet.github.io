# General information about porting software to IRIX

## Where to find symbols

### struct timeval

A bit surprisingly, this is easiest found in `<sys/resource.h>`

## Miscellaneous information

### _SGIAPI, _ABIAPI etc

There's an explanation on how these work in `<sys/standards.h>`
comments.
