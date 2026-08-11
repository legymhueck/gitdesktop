# Maintainer: MicLeh <micleh at proton dot me>
pkgname=gitdesktop
pkgver=0.8.0
pkgrel=1
pkgdesc="AI-native, keyboard-first Git desktop client (built from source)"
arch=('x86_64')
url="https://github.com/theBGuy/GitDesktop"
license=('Apache-2.0')
depends=('gtk3' 'webkit2gtk-4.1' 'libappindicator-gtk3' 'libayatana-appindicator' 'openssl' 'hicolor-icon-theme')
makedepends=('rust' 'nodejs' 'pnpm' 'pkgconf')
optdepends=('git: core version control'
            'github-cli: GitHub PR/Actions integration'
            'glab: GitLab integration')
conflicts=('gitdesktop-bin')
options=(!lto !debug)
source=("$pkgname-$pkgver.tar.gz::https://github.com/theBGuy/GitDesktop/archive/refs/tags/v$pkgver.tar.gz")
sha256sums=('SKIP')

prepare() {
    cd "GitDesktop-$pkgver"
    # deactivate tauri build
    sed -i 's/"active": true/"active": false/' src-tauri/tauri.conf.json
}

build() {
    cd "GitDesktop-$pkgver"

    # cleanup LDFLAGS for ring/lld compatibility
    export LDFLAGS="${LDFLAGS//-Wl,-O1/}"
    export LDFLAGS="${LDFLAGS//-Wl,--strip-debug/}"

    pnpm install
    # pnpm tauri build = pnpm build (Frontend) + cargo build --release (backend with embedded frontend)
    pnpm tauri build
}

package() {
    cd "GitDesktop-$pkgver"

    install -Dm755 "src-tauri/target/release/gitdesktop" "$pkgdir/usr/bin/gitdesktop"

    install -Dm644 "src-tauri/icons/32x32.png" "$pkgdir/usr/share/icons/hicolor/32x32/apps/gitdesktop.png"
    install -Dm644 "src-tauri/icons/128x128.png" "$pkgdir/usr/share/icons/hicolor/128x128/apps/gitdesktop.png"
    install -Dm644 "src-tauri/icons/128x128@2x.png" "$pkgdir/usr/share/icons/hicolor/256x256/apps/gitdesktop.png"

    install -Dm644 /dev/stdin "$pkgdir/usr/share/applications/gitdesktop.desktop" <<'EOF'
[Desktop Entry]
Name=GitDesktop
Comment=AI-native, keyboard-first Git desktop client
Exec=/usr/bin/gitdesktop %U
Icon=gitdesktop
Type=Application
Categories=Development;RevisionControl;
Terminal=false
StartupNotify=true
EOF
}