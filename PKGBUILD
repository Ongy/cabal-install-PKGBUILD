pkgname="cabal-install-git"
pkgver=Cabal.1.20.0.0.release.r3843.g0f41001bd
pkgrel=1

pkgdesc="The command-line interface for Cabal and Hackage."
arch=('i686' 'x86_64')
url="https://hackage.haskell.org/package/cabal-install"
license=('custom:BSD3')
depends=('gmp' 'zlib' 'libffi')
makedepends=('ghc' 'git' 'cabal-install')
provides=('cabal-install')
conflicts=('cabal-install')

source=('git://github.com/haskell/cabal')
md5sums=('SKIP')

SANDBOX="--package-db=../.cabal-sandbox/$(uname -m)-linux-$(readlink $(which ghc))-packages.conf.d"

pkgver() {
	cd "cabal"
	git describe --long | sed 's/\([^-]*-g\)/r\1/;s/-/./g'
}

build() {
	CONFIGF="${srcdir}/cabal/.cabal/config"
	SANDCONFIG="--sandbox-config-file=${srcdir}/cabal/cabal.sandbox.config"
	CCONFIG="--config=${CONFIGF}"

	mkdir -p "${srcdir}/cabal/.cabal/"
	cd "${srcdir}/cabal"
	cabal "${CCONFIG}" sandbox init

	# We need to update the package list, but do it locally
	TMP="${srcdir}/cabal/.cabal/package"
	# So we tell cabal to use a local package cache
	PPATH=${TMP//\//\\\/}

	#The sandbox init also creates the default config file for us
	sed "s/\\/\\/.cabal\\/packages/${PPATH}/" -i "${CONFIGF}"

	cd "./Cabal"
	cabal "${CCONFIG}" "${SANDCONFIG}" install

	cd "../cabal-install"
	cabal "${CCONFIG}" "${SANDCONFIG}" update
	cabal "${CCONFIG}" "${SANDCONFIG}" install --dependencies-only

	runhaskell Setup.hs configure -O "${SANDBOX}" --prefix=/usr --docdir="/usr/share/doc/${pkgname}"
	runhaskell Setup.hs build
}

package() {
	cd "${srcdir}/cabal"
	install -Dm644 LICENSE "${pkgdir}/usr/share/licenses/cabal-install/LICENSE"

	cd "${srcdir}/cabal/cabal-install"
	runhaskell Setup.hs copy --destdir="${pkgdir}"
}
