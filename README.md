# ripenhanced
Script to copy the data ("enhanced") portion of an audio cd to an ISO file

### Requirements
These packages will need to be installed before using this script.

* cdrdao
* ddrescue

### Usage
`ripenhanced {DEVICE} {ISOFILE}`

You can run this script multiple times if errors are found. Ddrescue will attempt to re-read the failed sectors.
