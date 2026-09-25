# SPDX-License-Identifier: AGPL-3.0

#    -----------------------------------------------------
#    Copyright © 2024, 2025, 2026  Pellegrino Prevete
#
#    All rights reserved
#    -----------------------------------------------------
#
#    This program is free software: you can redistribute
#    it and/or modify it under the terms of the
#    GNU Affero General Public License as published by
#    the Free Software Foundation, either version 3 of
#    the License, or (at your option) any later version.
#
#    This program is distributed in the hope that it
#    will be useful, but WITHOUT ANY WARRANTY;
#    without even the implied warranty of
#    MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.
#    See the GNU Affero General Public License for
#    more details.
#
#    You should have received a copy of the
#    GNU Affero General Public License
#    along with this program.
#    If not, see <https://www.gnu.org/licenses/>.

# Maintainers:
#   Truocolo
#     <truocolo@aol.com>
#     <truocolo@0x6E5163fC4BFc1511Dbe06bB605cc14a3e462332b>
#   Pellegrino Prevete (dvorak)
#     <pellegrinoprevete@gmail.com>
#     <dvorak@0x87003Bd6C074C713783df04f36517451fF34CBEf>
# Contributors:
#   Morten Linderud
#     <foxboron@archlinux.org>
#  Caleb Maclennan
#    <caleb@alerque.com>
#  Eli Schwartz
#    <eschwartz@archlinux.org>
#  Richard Bradfield
#    <bradfier@fstab.me>


_os="$(
  uname \
    -o)"
_arch="$(
  uname \
    -o)"
_go_pkg="go"
if [[ "${_os}" == "Android" ]]; then
  _libc="ndk-sysroot"
  _compiler="clang"
  _libcompiler="llvm-libs"
  if [[ "${_arch}" != "aarch64" ]] ;then
    _go_pkg="golang"
  fi
elif [[ "${_os}" == "GNU/Linux" ]]; then
  _libc="glibc"
  _compiler="gcc"
  _libcompiler="libgcc"
elif [[ "${_os}" == "Msys" ]]; then
  _libc="msys2-w32api-runtime"
  _libc_headers="msys2-w32api-headers"
  _compiler="gcc"
  _libcompiler="gcc-libs"
  _sh="sh"
else
  _msg=(
    "Unknown os '${_os}'."
  )
  msg \
    "${_msg[*]}"
  _libc="msys2-w32api-runtime"
  _libc_headers="msys2-w32api-headers"
  _compiler="gcc"
  _libcompiler="gcc-libs"
  _sh="sh"
fi
_pkg=github-cli
_pkg_alt=gh
pkgbase="${_pkg}"
pkgname=(
  "${_pkg}"
)
pkgver=2.101.0
pkgrel=16
pkgdesc="The GitHub CLI"
arch=(
  "aarch64"
  "arm"
  "armv6h"
  "armv7l"
  "armv8l"
  "i686"
  "mips"
  "pentium4"
  "powerpc"
  "x86_64"
)
url="https://github.com/cli/cli"
license=(
  "MIT"
)
depends=(
  "${_libc}"
)
if [[ "${_os}" != "Msys" ]]; then
  depends+=(
    "mailcap"
  )
fi
makedepends=(
  "${_go_pkg}"
)
_git="true"
if [[ "${_git}" == "true" ]]; then
  makedepends+=(
    "git"
  )
fi
provides=(
  "${_pkg_alt}=${pkgver}"
)
conflicts=(
  "${_pkg_alt}"
)
checkdepends=(
  "openssh"
)
_git_optdepends=(
  "git:"
    "To interact with repositories."
)
_org_freedesktop_secrets_optdepends=(
  "org.freedesktop.secrets:"
    "Store credentials in system keyring."
)
optdepends=(
  "${_git_optdepends[*]}"
  "${_org_freedesktop_secrets_optdepends[*]}"
)
options=(
  "!lto"
)
_tarname="${_pkg}-${pkgver}"
_tarfile="${_tarname}.tar.gz"
_url="${url}"
_uri="${_url}/archive/v${pkgver}.tar.gz"
_src="${_tarfile}::${_uri}"
_sum='a266fe8575c0e061b987920c1831a15f71bf0036a8729a5ebb93c2fb0164899c'
_patchname="telemetry-disable"
_patchfile="${_patchname}.patch"
_patch_commit="cb2509cb612cf9111a12a7960afd10b0c9d2dede"
_patch_uri="${url}/commit/${_patch_commit}.patch"
_patch_sum="f5c78941435a2cd7581b3ccc7f7c1f6db17e2e910bb6b87d1693ccb19ec0e7aa"
_patch_src="${_patchfile}"
if [[ ! -e "${_patchfile}" ]]; then
  _patch_src+="::${_patch_uri}"
fi
source=(
  "${_src}"
  "${_patch_src}"
)
sha256sums=(
  "${_sum}"
  "${_patch_sum}"
)

prepare() {
  cd \
    "cli-${pkgver}"
  # TODO:
  #   These tests invoke the TTY and
  #   our container *really* does not like that
  rm \
    "pkg/cmd/auth/login/login_test.go"
  # Drop tests that invoking 3rd party server processes
  rm \
    "pkg/cmd/search/shared/shared_test.go" \
    "internal/codespaces/rpc/invoker_test.go"
  # TODO:
  #   as-yet unmerged telemetry patch,
  #   see https://github.com/cli/cli/issues/13260
  patch \
    -p1 \
    -i \
    "${srcdir}/telemetry-disable.patch"
}

build() {
  local \
    _arch \
    _goflags=() \
    _go_build_tags=() \
    _make_opts=() \
    _target
  _arch="$(
    uname \
      -m)"
  _make_opts+=(
    GH_VERSION="v${pkgver}"
  )
  _go_flags+=(
    -trimpath
    -mod=readonly
    -modcacherw
  )
  if [[ "${_arch}" == "aarch64" || \
        "${_arch}" == "x86_64"  ]]; then
    _go_flags+=(
      -buildmode=pie
    )
  fi
  _go_build_tags+=(
    noupdateable
    notelemetry
  )
  _target="bin/gh"
  cd \
    "cli-$pkgver"
  if [[ "${_os}" == "Msys" ]]; then
    export \
      GOOS="windows"
    _target="${_target}.exe"
  fi
  export \
    CGO_CPPFLAGS="${CPPFLAGS}" \
    CGO_CFLAGS="${CFLAGS}" \
    CGO_CXXFLAGS="${CXXFLAGS}" \
    CGO_LDFLAGS="${LDFLAGS}" \
    GOFLAGS="${_go_flags[*]}" \
    GO_BUILDTAGS="$(
      IFS=","; \
      echo \
        "${_go_build_tags[*]}")"
  make \
    "${_make_opts[@]}" \
    "${_target}" \
    manpages
  "./bin/gh" \
    completion \
    -s \
      "bash" |
    install \
      -vDm0644 \
      "/dev/stdin" \
      "share/bash-completion/completions/gh"
  "./bin/gh" \
    completion \
    -s \
      "zsh" |
    install \
      -vDm0644 \
      "/dev/stdin" \
      "share/zsh/site-functions/_gh"
  "./bin/gh" \
    completion \
    -s \
      "fish" |
    install \
      -vDm0644 \
      "/dev/stdin" \
      "share/fish/vendor_completions.d/gh.fish"
}

check(){
  cd \
    "cli-$pkgver"
  make \
    test || \
  true
}

package() {
  local \
    _make_opts=()
  _make_opts+=(
    DESTDIR="${pkgdir}"
    prefix="/usr"
  )
  cd \
    "cli-$pkgver"
  make \
    "${_make_opts[@]}" \
    install
  cp \
    -r \
    "share/" \
    "${pkgdir}/usr"
  install \
    -vDm644 \
    "LICENSE" \
    "${pkgdir}/usr/share/licenses/${pkgname}/LICENSE"
  install \
    -vDm644 \
    "README.md" \
    "${pkgdir}/usr/share/doc/${pkgname}/README.md"
}
