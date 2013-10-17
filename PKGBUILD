# Maintainer: Alexander Kuznecov <alexander@kuznetcov.me>
_pkgname="nginx"
_user="http"
_group="http"
_doc_root="/usr/share/${_pkgname}/http"
_sysconf_path="etc"
_conf_path="${_sysconf_path}/${_pkgname}"
_tmp_path="/var/spool/${_pkgname}"
_pid_path="/run"
_lock_path="/var/lock"
_log_path="/var/log/${_pkgname}"

# 3d party modules versions:
_uploadprogress_ver="v0.8.4"
#_upstreamfair_hash="a18b4099fbd458111983200e098b6f0c8efed4bc"
_fancyindex_ver="master"
_httpupload_ver="2.2.0"
_push_module_ver="0.692"

pkgname=nginx-pushmodule-dev
pkgver=1.4.3
pkgrel=1
pkgdesc="lightweight HTTP server and IMAP/POP3 proxy server with standard, additional and 3d party modules - build for slact"
arch=('i686' 'x86_64')
install=install

depends=('pcre' 'zlib' 'openssl')

url="http://nginx.org"
license=('custom')
conflicts=('nginx' 'nginx-unstable' 'nginx-svn' 'nginx-devel' 'nginx-custom-dev' 'nginx-custom-slact') 
provides=('nginx' 'nginx-custom')
backup=("${_conf_path}/conf/nginx.conf"
  "${_conf_path}/conf/koi-win"
  "${_conf_path}/conf/koi-utf"
  "${_conf_path}/conf/win-utf"
  "${_conf_path}/conf/mime.types"
  "${_conf_path}/conf/fastcgi.conf"
  "${_conf_path}/conf/fastcgi_params"
  "${_conf_path}/conf/scgi_params"
  "${_conf_path}/conf/uwsgi_params"
  "etc/logrotate.d/nginx")
_user=http
_group=http

source=("http://nginx.org/download/nginx-$pkgver.tar.gz"
  "nginx.sh"
  "nginx.conf"
  "nginx.logrotate"
  "nginx.service")
md5sums=('4740dad2f0ed7f5fa16e8caaa8a9438e'
         '3d5ff154f308ef7c844423e44ef9f4e1'
         '1fe7a3ca0773ce13f9f92e239a99f8b9'
         'ab1eb640c978536c1dad16674d6b3c3c'
         '62d494d23aef31d0b867161f9fffa6eb')

build() {
  local _src_dir="${srcdir}/${_pkgname}-${pkgver}"
  local _build_dir="${_src_dir}/objs"
  local _pushmodule_dirname="nhpm"
  
  cd $_src_dir

  CFLAGS="-ggdb -O0" ./configure \
    --prefix="/${_conf_path}" \
    --sbin-path="/usr/bin/${_pkgname}" \
    --pid-path="${_pid_path}/${_pkgname}.pid" \
    --lock-path=${_pid_path}/${_pkgname}.lock \
    --http-client-body-temp-path=${_tmp_path}/client_body_temp \
    --http-proxy-temp-path=${_tmp_path}/proxy_temp \
    --http-fastcgi-temp-path=${_tmp_path}/fastcgi_temp \
    --http-uwsgi-temp-path=${_tmp_path}/uwsgi_temp \
    --http-scgi-temp-path=${_tmp_path}scgi_temp \
    --http-log-path=${_log_path}/access.log \
    --error-log-path=${_log_path}/error.log \
    --user=${_user} \
    --group=${_group} \
    --with-debug \
    --add-module=${startdir}/nhpm \

  make
}

package() {
  cd "${srcdir}/${_pkgname}-${pkgver}"
  make DESTDIR="$pkgdir/" install
  
  sed -i -e "s/\<user\s\+\w\+;/user $_user;/g" $pkgdir/$_conf_path/conf/nginx.conf
  
  install -d "${pkgdir}/${_tmp_path}"
  install -d "${pkgdir}/${_doc_root}"
  
  mv "${pkgdir}/${_conf_path}/html/"* "${pkgdir}/${_doc_root}"
  rm -rf "${pkgdir}/${_conf_path}/html"
  
  #install -D -m644 "${srcdir}/nginx.logrotate" "${pkgdir}/etc/logrotate.d/${_pkgname}" #no default logrotate.
  install -D -m644 "${srcdir}/nginx.conf" "${pkgdir}/etc/conf.d/${_pkgname}"
  install -D -m644 "${srcdir}/nginx.service" "${pkgdir}/lib/systemd/system/nginx.service"
  install -D -m644 "LICENSE" "${pkgdir}/usr/share/licenses/${_pkgname}/LICENSE"
  install -D -m644 "man/nginx.8" "${pkgdir}/usr/share/man/man8/nginx.8"
}
