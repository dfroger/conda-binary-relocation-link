conda-binary-relocation-link
============================

Experiment with conda binary relocation in case of links

Using conda
----------------------------

Build a conda package:

    conda build conda

There is three copy of the executable:

    $ find /local/froger/miniconda/envs/_build/ -type f -executable -exec ls -l {} \;
    -rwxr-xr-x 1 froger sed 8465 2015-01-05 11:06 /local/froger/miniconda/envs/_build/bin/foo
    -rwxr-xr-x 1 froger sed 8465 2015-01-05 11:06 /local/froger/miniconda/envs/_build/libexec/foo-core/foo-cmd0
    -rwxr-xr-x 1 froger sed 8465 2015-01-05 11:06 /local/froger/miniconda/envs/_build/libexec/foo-core/foo-cmd1

Reproduce manually
----------------------------

Manually build and install:

    $ ./configure --prefix=$PWD/foo_install
    $ make
    $ make install

Each executable is a reference to the same inode (link):

    $ find foo_install/ -type f -executable -exec ls -l {} \;
    -rwxr-xr-x 3 froger sed 8465 2015-01-05 11:03 foo_install/bin/foo
    -rwxr-xr-x 3 froger sed 8465 2015-01-05 11:03 foo_install/libexec/foo-core/foo-cmd0
    -rwxr-xr-x 3 froger sed 8465 2015-01-05 11:03 foo_install/libexec/foo-core/foo-cmd1

Change rpath:

    $ patchelf --set-rpath '$ORIGIN/../lib' foo_install/bin/foo
    $ patchelf --set-rpath '$ORIGIN/../lib' foo_install/libexec/foo-core/foo-cmd0
    $ patchelf --set-rpath '$ORIGIN/../lib' foo_install/libexec/foo-core/foo-cmd1

There is three copy of the executable:

    $ find foo_install/ -type f -executable -exec ls -l {} \;
    -rwxr-xr-x 1 froger sed 12561 2015-01-05 11:35 foo_install/bin/foo
    -rwxr-xr-x 1 froger sed 12561 2015-01-05 11:35 foo_install/libexec/foo-core/foo-cmd0
    -rwxr-xr-x 1 froger sed 12561 2015-01-05 11:35 foo_install/libexec/foo-core/foo-cmd1

Clean:

    $ make clean
    $ rm -rf foo_install

patchelf
----------------------------

patchelf breaks hardlinks:

    https://bugs.debian.org/cgi-bin/bugreport.cgi?bug=759497
    https://github.com/NixOS/patchelf/issues/32
