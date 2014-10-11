dylib-test
==========

Test cabal's --enable-executable-dynamic build on OSX (Mavericks).

# Cabal version
Cloned from GitHub (commit:104c6852ffa140cc795c3cb55c92d961c2f8e6d0)

# Symptom 

Build with `cabal configure --enable-executable-dynamic && cabal build`, created executable has a depenency to locally built `*.dylib ` but embedded `LC_RPATH` value is incorrect for installing `${HOME}/.cabal`

## Examine created executable
```
$ otool -L dist/build/dylib-test/dylib-test

dist/build/dylib-test/dylib-test:
	@rpath/libHSdylib-test-0.1.0.0-ghc7.8.3.dylib (compatibility version 0.0.0, current version 0.0.0)
	@rpath/libHSbase-4.7.0.1-ghc7.8.3.dylib (compatibility version 0.0.0, current version 0.0.0)
	@rpath/libHSinteger-gmp-0.5.1.0-ghc7.8.3.dylib (compatibility version 0.0.0, current version 0.0.0)
	@rpath/libHSghc-prim-0.3.1.0-ghc7.8.3.dylib (compatibility version 0.0.0, current version 0.0.0)
	@rpath/libHSrts-ghc7.8.3.dylib (compatibility version 0.0.0, current version 0.0.0)
	@rpath/libffi.dylib (compatibility version 7.0.0, current version 7.0.0)
	/usr/lib/libiconv.2.dylib (compatibility version 7.0.0, current version 7.0.0)
	/usr/lib/libSystem.B.dylib (compatibility version 1.0.0, current version 1197.1.1)

```


Below is `otool -l` output. `Load command 20`'s LC_RPATH considered to be incorrect (when copied to `${HOME}/.cabal` or else), right?

```
$ otool -l dist/build/dylib-test/dylib-test

dist/build/dylib-test/dylib-test:
Load command 0
      cmd LC_SEGMENT_64
  cmdsize 72
  segname __PAGEZERO
   vmaddr 0x0000000000000000
   vmsize 0x0000000100000000
  fileoff 0
 filesize 0
  maxprot 0x00000000
 initprot 0x00000000
   nsects 0
    flags 0x0
Load command 1
      cmd LC_SEGMENT_64
  cmdsize 472
  segname __TEXT
   vmaddr 0x0000000100000000
   vmsize 0x0000000000001000
  fileoff 0
 filesize 4096
  maxprot 0x00000007
 initprot 0x00000005
   nsects 5
    flags 0x0
Section
  sectname __text
   segname __TEXT
      addr 0x0000000100000ca0
      size 0x00000000000002e6
    offset 3232
     align 2^4 (16)
    reloff 0
    nreloc 0
     flags 0x80000400
 reserved1 0
 reserved2 0
Section
  sectname __stubs
   segname __TEXT
      addr 0x0000000100000f86
      size 0x000000000000000c
    offset 3974
     align 2^1 (2)
    reloff 0
    nreloc 0
     flags 0x80000408
 reserved1 0 (index into indirect symbol table)
 reserved2 6 (size of stubs)
Section
  sectname __stub_helper
   segname __TEXT
      addr 0x0000000100000f94
      size 0x0000000000000024
    offset 3988
     align 2^2 (4)
    reloff 0
    nreloc 0
     flags 0x80000400
 reserved1 0
 reserved2 0
Section
  sectname __const
   segname __TEXT
      addr 0x0000000100000fb8
      size 0x0000000000000024
    offset 4024
     align 2^3 (8)
    reloff 0
    nreloc 0
     flags 0x00000000
 reserved1 0
 reserved2 0
Section
  sectname __eh_frame
   segname __TEXT
      addr 0x0000000100000fe0
      size 0x0000000000000018
    offset 4064
     align 2^3 (8)
    reloff 0
    nreloc 0
     flags 0x00000000
 reserved1 0
 reserved2 0
Load command 2
      cmd LC_SEGMENT_64
  cmdsize 472
  segname __DATA
   vmaddr 0x0000000100001000
   vmsize 0x0000000000001000
  fileoff 4096
 filesize 4096
  maxprot 0x00000007
 initprot 0x00000003
   nsects 5
    flags 0x0
Section
  sectname __got
   segname __DATA
      addr 0x0000000100001000
      size 0x0000000000000040
    offset 4096
     align 2^3 (8)
    reloff 0
    nreloc 0
     flags 0x00000006
 reserved1 2 (index into indirect symbol table)
 reserved2 0
Section
  sectname __nl_symbol_ptr
   segname __DATA
      addr 0x0000000100001040
      size 0x0000000000000010
    offset 4160
     align 2^3 (8)
    reloff 0
    nreloc 0
     flags 0x00000006
 reserved1 10 (index into indirect symbol table)
 reserved2 0
Section
  sectname __la_symbol_ptr
   segname __DATA
      addr 0x0000000100001050
      size 0x0000000000000010
    offset 4176
     align 2^3 (8)
    reloff 0
    nreloc 0
     flags 0x00000007
 reserved1 12 (index into indirect symbol table)
 reserved2 0
Section
  sectname __const
   segname __DATA
      addr 0x0000000100001060
      size 0x0000000000000058
    offset 4192
     align 2^3 (8)
    reloff 0
    nreloc 0
     flags 0x00000000
 reserved1 0
 reserved2 0
Section
  sectname __data
   segname __DATA
      addr 0x00000001000010b8
      size 0x00000000000000a0
    offset 4280
     align 2^3 (8)
    reloff 0
    nreloc 0
     flags 0x00000000
 reserved1 0
 reserved2 0
Load command 3
      cmd LC_SEGMENT_64
  cmdsize 72
  segname __LINKEDIT
   vmaddr 0x0000000100002000
   vmsize 0x0000000000002000
  fileoff 8192
 filesize 5032
  maxprot 0x00000007
 initprot 0x00000001
   nsects 0
    flags 0x0
Load command 4
            cmd LC_DYLD_INFO_ONLY
        cmdsize 48
     rebase_off 8192
    rebase_size 24
       bind_off 8216
      bind_size 432
  weak_bind_off 0
 weak_bind_size 0
  lazy_bind_off 8648
 lazy_bind_size 32
     export_off 8680
    export_size 400
Load command 5
     cmd LC_SYMTAB
 cmdsize 24
  symoff 9360
   nsyms 83
  stroff 10744
 strsize 2480
Load command 6
            cmd LC_DYSYMTAB
        cmdsize 80
      ilocalsym 0
      nlocalsym 13
     iextdefsym 13
     nextdefsym 20
      iundefsym 33
      nundefsym 50
         tocoff 0
           ntoc 0
      modtaboff 0
        nmodtab 0
   extrefsymoff 0
    nextrefsyms 0
 indirectsymoff 10688
  nindirectsyms 14
      extreloff 0
        nextrel 0
      locreloff 0
        nlocrel 0
Load command 7
          cmd LC_LOAD_DYLINKER
      cmdsize 32
         name /usr/lib/dyld (offset 12)
Load command 8
     cmd LC_UUID
 cmdsize 24
    uuid CEC0D2E2-AAF0-38A3-AA43-9AB0EC47D152
Load command 9
      cmd LC_VERSION_MIN_MACOSX
  cmdsize 16
  version 10.9
      sdk 10.9
Load command 10
      cmd LC_SOURCE_VERSION
  cmdsize 16
  version 0.0
Load command 11
       cmd LC_MAIN
   cmdsize 24
  entryoff 3856
 stacksize 0
Load command 12
          cmd LC_LOAD_DYLIB
      cmdsize 72
         name @rpath/libHSdylib-test-0.1.0.0-ghc7.8.3.dylib (offset 24)
   time stamp 2 Thu Jan  1 09:00:02 1970
      current version 0.0.0
compatibility version 0.0.0
Load command 13
          cmd LC_LOAD_DYLIB
      cmdsize 64
         name @rpath/libHSbase-4.7.0.1-ghc7.8.3.dylib (offset 24)
   time stamp 2 Thu Jan  1 09:00:02 1970
      current version 0.0.0
compatibility version 0.0.0
Load command 14
          cmd LC_LOAD_DYLIB
      cmdsize 72
         name @rpath/libHSinteger-gmp-0.5.1.0-ghc7.8.3.dylib (offset 24)
   time stamp 2 Thu Jan  1 09:00:02 1970
      current version 0.0.0
compatibility version 0.0.0
Load command 15
          cmd LC_LOAD_DYLIB
      cmdsize 72
         name @rpath/libHSghc-prim-0.3.1.0-ghc7.8.3.dylib (offset 24)
   time stamp 2 Thu Jan  1 09:00:02 1970
      current version 0.0.0
compatibility version 0.0.0
Load command 16
          cmd LC_LOAD_DYLIB
      cmdsize 56
         name @rpath/libHSrts-ghc7.8.3.dylib (offset 24)
   time stamp 2 Thu Jan  1 09:00:02 1970
      current version 0.0.0
compatibility version 0.0.0
Load command 17
          cmd LC_LOAD_DYLIB
      cmdsize 48
         name @rpath/libffi.dylib (offset 24)
   time stamp 2 Thu Jan  1 09:00:02 1970
      current version 7.0.0
compatibility version 7.0.0
Load command 18
          cmd LC_LOAD_DYLIB
      cmdsize 56
         name /usr/lib/libiconv.2.dylib (offset 24)
   time stamp 2 Thu Jan  1 09:00:02 1970
      current version 7.0.0
compatibility version 7.0.0
Load command 19
          cmd LC_LOAD_DYLIB
      cmdsize 56
         name /usr/lib/libSystem.B.dylib (offset 24)
   time stamp 2 Thu Jan  1 09:00:02 1970
      current version 1197.1.1
compatibility version 1.0.0
Load command 20
          cmd LC_RPATH
      cmdsize 72
         path /Users/objectx/Workspace/GitHub/dylib-test/dist/build (offset 12)
Load command 21
          cmd LC_RPATH
      cmdsize 56
         path /opt/ghc-7.8/lib/ghc-7.8.3/base-4.7.0.1 (offset 12)
Load command 22
          cmd LC_RPATH
      cmdsize 64
         path /opt/ghc-7.8/lib/ghc-7.8.3/integer-gmp-0.5.1.0 (offset 12)
Load command 23
          cmd LC_RPATH
      cmdsize 56
         path /opt/ghc-7.8/lib/ghc-7.8.3/ghc-prim-0.3.1.0 (offset 12)
Load command 24
          cmd LC_RPATH
      cmdsize 48
         path /opt/ghc-7.8/lib/ghc-7.8.3/rts-1.0 (offset 12)
Load command 25
      cmd LC_FUNCTION_STARTS
  cmdsize 16
  dataoff 9080
 datasize 24
Load command 26
      cmd LC_DATA_IN_CODE
  cmdsize 16
  dataoff 9104
 datasize 0
Load command 27
      cmd LC_DYLIB_CODE_SIGN_DRS
  cmdsize 16
  dataoff 9104
 datasize 256
```
