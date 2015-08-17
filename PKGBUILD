# Maintainer: Alexander Kuznecov <akuznecov@adyax.com>

_pkgname="varnish"
_user="http"
_group="http"
_sysconf_path="etc"
_conf_path="${_sysconf_path}/${_pkgname}"
_daemonconf_path="${_sysconf_path}/conf.d/${_pkgname}"
_binary_path="usr/bin"
_lib_path="var/lib"
_vmod_path="usr/lib/varnish/vmods"

# VMODs:
_vmod_list="authentication geoip header boltsort querystring var timers softpurge shield timeutils cookie"

pkgname=varnish-custom
pkgver=3.0.5
pkgrel=1
pkgdesc="Varnish - high-performance HTTP accelerator with additional VMODs"
arch=('i686' 'x86_64')

depends=('gcc' 'libedit' 'pcre' 'ncurses')
makedepends=('git' 'varnish' 'python' 'python-docutils')

url="https://www.varnish-cache.org/"
license=('custom')
conflicts=('varnish' 'varnish-git') 
provides=('varnish')
backup=("${_conf_path}/default.vcl"
	"${_daemonconf_path}"
	"usr/lib/systemd/system/${_pkgname}.service"
	)

source=("http://repo.varnish-cache.org/source/${_pkgname}-${pkgver}.tar.gz"
        "default.vcl"
        "${_pkgname}.rc"
        "${_pkgname}.conf"
        "${_pkgname}.service"
        "${_pkgname}-vcl-reload"
        "git://github.com/lampeh/libvmod-geoip.git"
        "git://github.com/pariahsoft/libvmod-authentication.git"
        "git://github.com/varnish/libvmod-header.git"
        "git://github.com/vimeo/libvmod-boltsort.git"
        "git://github.com/Dridi/libvmod-querystring.git"
        "git://github.com/varnish/libvmod-var.git"
        "git://github.com/jib/libvmod-timers.git"
        "git://github.com/lkarsten/libvmod-softpurge.git"
        "git://github.com/varnish/libvmod-shield.git"
        "git://github.com/jthomerson/libvmod-timeutils.git"
        "git://github.com/lkarsten/libvmod-cookie.git"
)

md5sums=('674d44775cc927aee4601edb37f60198'
         'd128b6e3ec949039e845a0a5d0e5f88c'
         '40b4c83b3ad225ed2f4bd7e677fe41a2'
         '1358a37517d963e8a1840264caecd1cb'
         'a59b17d8e3066abcf3dd7755a2212dd4'
         '6ff7c235191a0b7cdd7c384019aeb2bd'
         'SKIP'
         'SKIP'
         'SKIP'
         'SKIP'
         'SKIP'
         'SKIP'
         'SKIP'
         'SKIP'
         'SKIP'
         'SKIP'
         'SKIP')



build() {

  # Varnish
	cd "${_pkgname}-${pkgver}"
  ./configure \
    --prefix=/usr \
    --sysconfdir=/${_sysconf_path} \
    --localstatedir=/${_lib_path} \
    --sbindir=/${_binary_path}
	make

  # VMODs
  for _vmod_item in ${_vmod_list}; do
    cd "${srcdir}/libvmod-${_vmod_item}"
    ./autogen.sh
    ./configure VMODDIR=/${_vmod_path} VARNISHSRC=${srcdir}/${_pkgname}-${pkgver} --prefix=/usr
    make
  done

}


check() {

  # VMODs
  for _vmod_item in ${_vmod_list}; do
    cd "${srcdir}"/libvmod-${_vmod_item}
    varnishtest -D vmod_topbuild="$srcdir"/libvmod-${_vmod_item}/src/tests/*.vtc || true
  done

}



package() {

  # Varnish
	make -C "${_pkgname}-${pkgver}" DESTDIR="${pkgdir}" install
  install -m755 "${srcdir}/varnish-vcl-reload" "${pkgdir}/${_binary_path}"
  install -Dm644 "${srcdir}/default.vcl" "${pkgdir}/${_sysconf_path}/${_pkgname}/default.vcl"
  install -Dm644 "${srcdir}/${_pkgname}.service" "${pkgdir}/usr/lib/systemd/system/${_pkgname}.service"
  install -Dm755 "${srcdir}/${_pkgname}.rc" "${pkgdir}/${_sysconf_path}/rc.d/${_pkgname}"
  install -Dm644 "${srcdir}/${_pkgname}.conf" "${pkgdir}/${_daemonconf_path}"
  install -Dm644 "${_pkgname}-${pkgver}/LICENSE" "${pkgdir}/usr/share/licenses/${_pkgname}/LICENSE"

  # VMODs
  for _vmod_item in ${_vmod_list}; do
    cd "${srcdir}"/libvmod-${_vmod_item}
    make DESTDIR="${pkgdir}/" install
  done

}
