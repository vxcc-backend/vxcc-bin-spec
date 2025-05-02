# vxcc binary format
new modern linker object format.

## design goals
- can be linked by linkers without them knowing the target architecture. that is done via [configurable relocs](#configurable-relocs)
- allows for complex link-time evaluation, like performing math on the address of a external symbol. that is done via [linker vm](#linker-vm)
- stable, extensible, and versatile binary format
- allows for automatic (at link time) moving of symbols, and insertion of indirect jumps to make all code work on architectures with only short jumps.
- configurable relocs can specify multiple alternatives (for example short and longer jump variants), which the linker selects based on lowest binary size.
- binary format allows for storage of different LTO formats. when those are used however, compatibility with multiple linker implementations gets reduced.
- different section types, flags (thread local, ...), and permissions (read, write, execute, ...)
- operatin systems should be able to quickly load and run files in this format if they are already fully linked.

## configurable relocs

## linker VM
the linker VM byecode itself has relocs too, to reference other external symbol.

code sections in the binary can reloc against linker VM variables.
