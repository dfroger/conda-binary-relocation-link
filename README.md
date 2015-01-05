conda-binary-relocation-link
============================

Experiment with conda binary relocation in case of links

Manually build and install:

    ./configure --prefix=$PWD/foo_install
    make
    make install

Each executable are links to the same inode:

    $ find foo_install/ -type f -executable -exec ls -l {} \;
    -rwxr-xr-x 3 froger sed 8465 2015-01-05 11:03 foo_install/bin/foo
    -rwxr-xr-x 3 froger sed 8465 2015-01-05 11:03 foo_install/libexec/foo-core/foo-cmd0
    -rwxr-xr-x 3 froger sed 8465 2015-01-05 11:03 foo_install/libexec/foo-core/foo-cmd1

Clean:

    make clean
    rm -rf foo_install

Build a conda package:

    conda build conda

There is three copy of the executable:

    $ find /local/froger/miniconda/envs/_build/ -type f -executable -exec ls -l {} \;
