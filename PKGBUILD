# Maintainer: Pierre Neidhardt <ambrevar at gmail dot com>
# Original PKGBUILD: Mark Foxwell <fastfret79@archlinux.org.uk>

pkgname=msmtp-pwpatched
pkgver=20120706
pkgrel=2
pkgdesc='An SMTP client. Includes pwpatch for --password parameter.'
arch=(i686 x86_64)
license=(GPL3)
url=http://msmtp.sourceforge.net
depends=(gnutls libidn)
makedepends=(git)
provides=(msmtp smtp-forwarder)
optdepends=('libgnome-keyring: External Authentication')
conflicts=(msmtp)
install=msmtp.install
source=(msmtp.patch)
sha1sums=('21e7ba2c4176e28b1b2debc755e47957102230ad')

_gitroot=git://msmtp.git.sourceforge.net/gitroot/msmtp/msmtp
_gitname=msmtp

build() {
  msg 'Connecting to GIT server...'

  if [[ -d $_gitname ]]; then
    ( cd $_gitname; git pull origin )
    msg 'The local files are updated.'
  else
    git clone $_gitroot $_gitname
  fi

  msg "GIT checkout done or server timeout"
  msg "Starting make..."

  rm -rf $_gitname-build
  git clone $_gitname{,-build}
  cd $_gitname-build

  autoreconf
  ./configure         \
    --prefix=/usr     \
    --sysconfdir=/etc \
  
  make
}

package() {
  cd $_gitname-build

  # Patch for --password parameter support.
  patch -p1 -i "${srcdir}/msmtp.patch"
  
  make DESTDIR=$pkgdir install

  install -Dm644 scripts/vim/msmtp.vim \
    $pkgdir/usr/share/vim/vimfiles/syntax/msmtp.vim

  sed -i "s:LOG=~/log/msmtp.queue.log:LOG=~/.msmtp.queue/msmtp.queue.log:g" scripts/msmtpq/msmtpq

  for _i in msmtp{q,-queue}; do
    install -D scripts/msmtpq/$_i $pkgdir/usr/bin/$_i
  done

  install -Dm644 scripts/msmtpq/README.msmtpq \
    $pkgdir/usr/share/doc/msmtp/README.msmtpq
  for _i in msmtprc-{system,user}.example; do
    install -Dm644 doc/$_i $pkgdir/usr/share/doc/msmtp/$_i
  done
  
}

