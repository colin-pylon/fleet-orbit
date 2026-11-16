pkgname=fleet-orbit
pkgver=1.63.0
pkgrel=1
pkgdesc='Eases the deployment of osquery connected with a Fleet server.'
arch=(x86_64)
url='https://github.com/fleetdm/fleet/tree/main/orbit'
license=(MIT)
depends=(osquery)
optdepends=(
  'fleet-desktop: integration with Fleet Desktop applet'
)
makedepends=(go)
backup=(etc/default/fleet-orbit)
optdepends=()
source=(
  'https://github.com/fleetdm/fleet/archive/refs/tags/orbit-v1.63.0.tar.gz'
  'fleet-orbit-cleanup.hook'
  'fleet-orbit-config'
  'fleet-orbit.service'
)
sha512sums=('381eb6067c360668fb515fd43be85f756204254303cad201b4edf1751c6a25c2293744ceed1e3aa0d0c4d0a45e5c82e8fe021e3309ba51b3a17f6046586f09fa'
            'c4d4fdf980a891f5e56ca82173c57b60d0e157ef4af769fc5d9ecd7b9c70124402d694f35d48101e6633d0134ade9ab33cff3c129e2f603a6b7df1ee560eab5a'
            'f6c5c58261eca4f8bd49e3e96a56c091378deadceba5329666bda9d1754a8a07a28793d9d31dee3d429eb7ada1e09548264ca0f5e94ba18577ceff530ff8e5e2'
            '9cfbc3823257c625f16eb4abd7ef19b4ca52856fe8b920a28357196fa27a581a9c891db27bf422deae113eaa13e90224e03dab5770186d17d8ce056f167ed9f4')

install=fleet-orbit.install

build() {
  cd "${pkgname}-v${pkgver}"

  mkdir -p build
  go mod download

  isodate="$(TZ=UTC date +'%Y-%m-%dT%H:%M:%SZ')"
  importpath='github.com/fleetdm/fleet/v4/orbit/pkg/build'
  commitsha='5e2f9163460ac8246bbace9192bcb99e8a2989e3'

  ldflags="-s -w"
  ldflags="${ldflags} -X ${importpath}.Version=v${pkgver}"
  ldflags="${ldflags} -X ${importpath}.Commit=${commitsha}"
  ldflags="${ldflags} -X ${importpath}.Date=${isodate}"

  CGO_ENABLED=1 go build \
    -o build/ \
    -trimpath \
    -buildvcs=false \
    -ldflags="${ldflags}" \
    ./orbit/cmd/orbit/
}

package() {
  install -Dm644 'fleet-orbit-config' "${pkgdir}/etc/default/fleet-orbit"
  install -Dm644 'fleet-orbit.service' "${pkgdir}/usr/lib/systemd/system/fleet-orbit.service"
  install -Dm644 'fleet-orbit-cleanup.hook' "${pkgdir}/usr/share/libalpm/hooks/fleet-orbit-cleanup.hook"

  install -Dm644 "${pkgname}-v${pkgver}/orbit/LICENSE" "${pkgdir}/usr/share/licenses/${pkgname}/LICENSE"

  install -Dm755 "${pkgname}-v${pkgver}/build/orbit" "${pkgdir}/opt/fleet-orbit/bin/orbit/linux/stable/orbit"
  ln -s "./linux/stable/orbit" "${pkgdir}/opt/fleet-orbit/bin/orbit/orbit"

  mkdir -p "${pkgdir}/opt/fleet-orbit/bin/osqueryd/linux/stable/"
  ln -s /usr/bin/osqueryd "${pkgdir}/opt/fleet-orbit/bin/osqueryd/linux/stable/osqueryd"
}