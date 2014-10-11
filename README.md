dylib-test
==========

Test cabal's --enable-executable-dynamic build

# Symptom 

Build with `cabal configure --enable-executable-dynamic && cabal build`, created executable has a depenency to locally built *.dylib but embedded LC_RPATH value is incorrect for installing `${HOME}/.cabal`
