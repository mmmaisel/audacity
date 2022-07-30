# Maintainer: David Runge <dvzrv@archlinux.org>
# Contributor: Eric Bélanger <eric@archlinux.org>
# Modified by: Max Maisel <max.maisel@posteo.de>

_name=Audacity
pkgbase=audacity
pkgname=(audacity-compressor2)
pkgver=3.2.1
pkgrel=1
pkgdesc="A program that lets you manipulate digital audio waveforms"
arch=(x86_64)
url="https://audacityteam.org"
license=(GPL3)
makedepends=(
  alsa-lib
  chrpath
  cmake
  ffmpeg
  flac
  gcc-libs
  glibc
  gdk-pixbuf2
  glib2
  gtk3
  jack
  lame
  libid3tag
  libmad
  libogg
  libsbsms
  libsndfile
  libsoxr
  libvorbis
  lilv
  lv2
  mpg123
  portaudio
  portmidi
  portsmf
  python
  soundtouch
  sqlite
  suil
  twolame
  wavpack
  wxwidgets-gtk3
  vamp-plugin-sdk
  vst3sdk
)
options=(debug)
source=(
  "git+https://github.com/mmmaisel/audacity.git#branch=realtime-compressor2-$pkgver"
  https://github.com/$pkgbase/$pkgbase/releases/download/$_name-$pkgver/$pkgbase-manual-$pkgver.tar.gz
)
sha512sums=('SKIP'
            '9c3021c8830e99ac063113a856b658be5813fcb7d24723c2316399d93447b0ef9d6ffcf1cea8e752f89a615e82aa651ebaac3f56cc78797b85a669c63da49188')
b2sums=('SKIP'
        '445568c3fe1728cf19c211917c58c5ce811af41773a5d1e37f6e320fe106be4261e1bdb5b2943b5bda2b5aa0543357d2a68a015b579d88c45dc6d368e4b7f385')

build() {
  local cmake_options=(
    -D CMAKE_BUILD_TYPE=None
    -D CMAKE_INSTALL_PREFIX=/usr
    -D AUDACITY_BUILD_LEVEL=2
    -D audacity_conan_enabled=OFF
    -D audacity_has_networking=OFF
    -D audacity_has_crashreports=OFF
    -D audacity_has_updates_check=OFF
    -D audacity_has_sentry_reporting=OFF
    -D audacity_lib_preference=system
    -D audacity_obey_system_dependencies=ON
    -D audacity_use_vst3sdk=system
  )

  export VST3SDK='/usr/share/vst3sdk'
  export CFLAGS+=" -DNDEBUG"
  export CXXFLAGS+=" -DNDEBUG"
  cmake "${cmake_options[@]}" -B build -S $pkgname-$_name-$pkgver -Wno-dev
  cmake --build build
}

check() {
  ctest --test-dir build --output-on-failure
}

package_audacity-compressor2() {
  license+=(BSD)
  groups=(pro-audio)
  depends=(
    alsa-lib libasound.so
    flac libFLAC.so libFLAC++.so
    gcc-libs
    glibc
    gdk-pixbuf2 libgdk_pixbuf-2.0.so
    glib2 libglib-2.0.so libgobject-2.0.so
    gtk3 libgdk-3.so libgtk-3.so
    jack libjack.so
    lame libmp3lame.so
    libid3tag libid3tag.so
    libmad
    libogg libogg.so
    libsbsms libsbsms.so
    libsndfile libsndfile.so
    libsoxr
    libvorbis libvorbis.so libvorbisenc.so libvorbisfile.so
    lilv liblilv-0.so
    mpg123 libmpg123.so
    portaudio libportaudio.so
    portmidi libportmidi.so
    portsmf libportSMF.so
    python
    soundtouch
    sqlite libsqlite3.so
    suil libsuil-0.so
    twolame libtwolame.so
    wxwidgets-gtk3
    vamp-plugin-sdk libvamp-hostsdk.so
    wavpack
  )
  provides=(audacity)
  conflicts=(audacity)
  optdepends=(
    'audacity-docs: for documentation'
    'ffmpeg: for additional import/export capabilities'
  )
  provides=(ladspa-host lv2-host vamp-host vst-host vst3-host)


  DESTDIR="$pkgdir" cmake --install build
  install -vDm 644 $pkgname-$_name-$pkgver/lib-src/libnyquist/nyquist/license.txt "$pkgdir/usr/share/licenses/$pkgname/LICENSE.nyquist"

  # remove unsafe RPATH: https://github.com/audacity/audacity/issues/3289
  (
  cd "$pkgdir/usr/lib/$pkgname"
  for _lib in *.so; do
    chrpath --delete "$_lib"
    chmod 755 "$_lib"
  done
  )
  (
  cd "$pkgdir/usr/lib/$pkgname/modules"
  for _lib in *.so; do
    chrpath --delete "$_lib"
    chmod 755 "$_lib"
  done
  )
  # NOTE: private libraries are public: https://github.com/audacity/audacity/issues/3812
}
