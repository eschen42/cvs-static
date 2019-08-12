# Sources for compiling statically linked CVS binary under Alpine/musl

This repository is for compiling the CVS binary statically linked to the musl libc library under Alpine Linux.  This repository updates elaborates upon [https://github.com/eschen42/alpine-cbuilder/blob/master/README.md#cvs-executable-independent-of-glibc](https://github.com/eschen42/alpine-cbuilder/blob/master/README.md#cvs-executable-independent-of-glibc).

The source code here originates from the CVS source distribution downloaded from [http://ftp.gnu.org/non-gnu/cvs/source/feature/1.12.13/](http://ftp.gnu.org/non-gnu/cvs/source/feature/1.12.13/).  The file COPYING indicates that this softwrae is available under the GNU Public License.  Very oddly, I cannot find a CVS repository for this version!

# How to build a statically linked CVS binary
## Running the `alpine-cbuilder` container

From [https://quay.io/repository/eschen42/alpine-cbuilder?tab=tags](https://quay.io/repository/eschen42/alpine-cbuilder?tab=tags), choose a tag for `alpine-cbuilder` docker image that passes the security scan; `alpine-cbuilder` is built from [https://github.com/eschen42/alpine-cbuilder](https://github.com/eschen42/alpine-cbuilder).  As security issues are discovered, images that formerly passed the scan lose their "passing" status, so you may wish to clone the `alpine-cbuilder` repository and build it locally, addressing the issues; ideally, you would send me a pull request.

For example, today tag `v0.3.2` passed security scan, so I would pull that version and run it.  Starting in the directory containing this README.md file:
```bash
docker pull quay.io/eschen42/alpine-cbuilder:v0.3.2
docker run --rm -ti -v `pwd`/src:/src quay.io/eschen42/alpine-cbuilder:v0.3.2 bash
```

## Building CVS

At the bash prompt opened above:
```bash
cd /src
# link statically to code from musl
export LDFLAGS='--static'
./configure
make
```
This produces `/src/cvs` within the container, which, outside the container, is `./src/cvs` relative to the directory containing this file.

Outside of Alpine, you can validate that the binary is statically linked with the command:
```bash
ldd ./src/cvs
```
(Within Alpine, the result confusingly does not say that the binary is statically linked, although it is.)

## Why use `musl` rather than `glibc`

Evidently, statically linking libaries against `glibc` does not result in complete static linking: see [https://github.com/rust-lang/cargo/issues/2968#issuecomment-238196762](https://github.com/rust-lang/cargo/issues/2968#issuecomment-238196762) and it runs up against licensing issues [https://lwn.net/Articles/117972/](https://lwn.net/Articles/117972/).  By contrast, `musl` carries the non-restrictive MIT Licence, and it makes completely static links.

