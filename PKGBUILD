# Maintainer: Baptiste Jonglez <baptiste--aur at jonglez dot org>
pkgname=lib32-ocaml
pkgver=3.12.0
pkgrel=1
pkgdesc="Ocaml native code compiler to generate linux 32-bit binaries on a 64-bit system."
arch=('x86_64')
url="http://caml.inria.fr/"
license=('LGPL2' 'custom: QPL-1.0')
depends=('ocaml')
makedepends=('lib32-libx11' 'gcc-multilib')
options=('')
source=(http://caml.inria.fr/distrib/ocaml-3.12/ocaml-$pkgver.tar.bz2)
md5sums=('bd92c8970767f412bc1e9a6c625b5ccf')

real_prefix="/usr"
pkg_prefix="$pkgdir/usr"

build() {
  cd "$srcdir/ocaml-$pkgver"

  ./configure \
      -no-tk \
      -bindir $real_prefix/bin \
      -libdir $real_prefix/lib32/ocaml \
      -mandir $real_prefix/share/man/man1 \
      -cc "gcc -m32" \
      -as "as --32" \
      -aspp "gcc -m32 -c" \
      -host i386-linux \
      -partialld "ld -r -melf_i386"

  # We only build what's needed to get the native compiler (saves _a lot_ of time...)
  make core

  # This builds the native libraries and ocamlopt
  make opt

}

package() {
  cd "$srcdir/ocaml-$pkgver"

  mkdir -p $pkg_prefix/bin
  mkdir -p $pkg_prefix/lib32/ocaml
  mkdir -p $pkg_prefix/lib32/ocaml/threads
  mkdir -p $pkg_prefix/lib32/ocaml/stublibs
  mkdir -p $pkg_prefix/lib32/ocaml/caml


  make_arg="BINDIR=$pkg_prefix/bin LIBDIR=$pkg_prefix/lib32/ocaml"

  # install some byte-code libraries, interfaces, etc...
  # this is actually the 'install' target of the Makefile, stripped
  # down to remove anything we don't need (toplevel, bytecode compiler, ...)
  (cd stdlib; make $make_arg install)
  for i in unix str num dynlink bigarray systhreads threads graph
  do
      (cd otherlibs/$i; make $make_arg install) 
  done
  cp config/Makefile $pkg_prefix/lib32/ocaml/Makefile.config


  # then install the native part (libraries, ocamlopt)
  make BINDIR=$pkg_prefix/bin LIBDIR=$pkg_prefix/lib32/ocaml installopt
  
  # we don't want to interfere with an existing ocaml install
  mv $pkgdir/usr/bin/ocamlopt $pkgdir/usr/bin/ocamlopt.32
}
