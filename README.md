# homebrew-os161

[Homebrew] formulae for installing [System/161] and an [OS/161]
cross-compiling toolchain on macOS.

This is a fork of [benesch/homebrew-os161] updated to get it working in 2024.

**Supported versions:**

* System/161 2.0.8
* OS/161 2.0.2
  * Binutils 2.24+os161-2.1
  * GCC 4.8.3+os161-2.1
  * GDB 7.8+os161-2.1

These packages are downloaded from the [official Harvard
sources][161-download].

## Quick start

### Intel

With [Homebrew] installed, run

```bash
$ brew tap liamolucko/os161
$ brew install os161-toolchain
```

then follow the standard OS/161 spinup guide.

### Apple Silicon

Installing on Apple Silicon is a bit tricker, because GCC and GDB don't support it[^1]. That means that you need to install the Intel versions, and run them through Rosetta.

To do that, you can install an Intel version of Homebrew alongside your Apple Silicon version, by adding `arch -x86_64` to the start of the regular Homebrew installer:

```bash
$ arch -x86_64 /bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```

Then you can follow the same instructions as for Intel machines, but using the absolute path of Intel Homebrew (since you've probably left Apple Silicon Homebrew as first in your `PATH`):

```bash
$ /usr/local/bin/brew tap liamolucko/os161
$ /usr/local/bin/brew install os161-toolchain
```

`os161-binutils` and `sys161` work just fine on Apple Silicon though, so feel free to install those using normal Homebrew and only use the Intel version for `os161-gcc` and `os161-gdb`.

[^1]: [There are patches to get GCC working][gcc-patches], which Homebrew automatically uses and so `brew install gcc` works fine; however, they only go down to GCC 6, and OS/161 uses GCC 4. GDB doesn't work at all.

## Detailed installation instructions

If you're using Apple Silicon, follow the above instructions to install Intel Homebrew and then replace `brew` with `/usr/local/bin/brew` in all these commands.

### Homebrew tap

Install Homebrew from http://brew.sh if you don't have it already.
Then, add this repository as a [custom tap] so Homebrew can find the
OS161-specific formula:

```bash
$ brew tap liamolucko/os161
```

### System/161

First, install the System/161 MIPS simulator.

```bash
$ brew install sys161
```

At this point, `sys161` will be available in your PATH and runnable. A
sample `sys161.conf` is installed to:

    /usr/local/share/examples/sys161/sys161.conf.sample


### GCC

Then, you need a cross-compiling GCC capable of building executables for
System/161:

```bash
$ brew install os161-gcc
```

This will install a GCC toolchain prefixed with `mips-harvard-os161-`
into your PATH. This means that directly invoking GCC is rather
annoying, as `mips-harvard-os161-gcc` is a lot to type. You'll rarely
need to invoke the compiler directly, though; the OS/161 Makefiles will
do this for you.


### GDB

You'll almost certainly want a GDB capable of debugging executables
built for System/161:

```bash
$ brew install os161-gdb
```

This installs `mips-harvard-os161-gdb`. Since you'll be invoking this
command manually quite a bit, this automatically installs a shorter
alias, `os161-gdb`.


## OS/161

To set up stock OS/161 in a nutshell:

```bash
# Obtain sources
$ wget http://os161.eecs.harvard.edu/download/os161-base-2.0.3.tar.gz
$ tar xf os161-base-2.0.3.tar.gz

# Build userland
$ cd os161-base-2.0.3
$ ./configure
$ bmake
$ bmake install

# Build kernel
$ cd kern/conf
$ ./config DUMBVM
$ cd ../compile/DUMBVM
$ bmake depend
$ bmake
$ bmake install

# Run kernel
$ cd ~/os161/root
$ cp /usr/local/share/examples/sys161/sys161.conf.sample sys161.conf
$ sys161 kernel
```

See the [OS/161 guides and resources] for next steps.


[Homebrew]: http://brew.sh
[System/161]: http://os161.eecs.harvard.edu/#sys161
[OS/161]: http://os161.eecs.harvard.edu/
[benesch/homebrew-os161]: https://github.com/benesch/homebrew-os161
[OS/161 guides and resources]: http://os161.eecs.harvard.edu/resources/
[161-download]: http://os161.eecs.harvard.edu/download/
[gcc-patches]: https://github.com/iains/gcc-13-branch
[custom tap]: https://github.com/Homebrew/homebrew/blob/master/share/doc/homebrew/brew-tap.md
