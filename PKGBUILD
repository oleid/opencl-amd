# Maintainer: grmat <grmat@sub.red>

pkgname=opencl-amd
pkgdesc="OpenCL userspace driver as provided in the amdgpu-pro driver stack. This package is intended to work along with the free amdgpu stack."
pkgver=17.20.445420
pkgrel=1
arch=('x86_64')
url='http://www.amd.com'
license=('custom:AMD')
makedepends=('wget')
depends=('ocl-icd')
conflicts=('amdgpocl')

DLAGENTS='https::/usr/bin/wget --referer https://support.amd.com/en-us/kb-articles/Pages/AMDGPU-PRO-Driver-for-Linux-Release-Notes.aspx -N %u'

prefix='amdgpu-pro-'
major='17.20'
minor='445420'
shared="opt/amdgpu-pro/lib/x86_64-linux-gnu"

source=("https://www2.ati.com/drivers/linux/ubuntu/${prefix}${major}-${minor}.tar.xz")
sha256sums=('44066abd68be933571e9a01cc2486d458e4250a22e477f9401bc883a5bc49512')

pkgver() {
	echo "${major}.${minor}"
}

unpack_deb() {
  ar x "$1"
  tar xvJf data.tar.xz
}

do_opencl()
{
	mkdir -p "${srcdir}/opencl"
	cd "${srcdir}/opencl"
	unpack_deb "${srcdir}/${prefix}${major}-${minor}/opencl-amdgpu-pro-icd_${major}-${minor}_amd64.deb"

	cd ${shared}
	sed -i "s|libdrm_amdgpu|libdrm_amdgpo|g" libamdocl64.so

	rm -r "${srcdir}/opencl"
}

do_drm_ids()
{
	mkdir -p "${srcdir}/libdrm"
	cd "${srcdir}/libdrm"

	unpack_deb "${srcdir}/${prefix}${major}-${minor}/ids-amdgpu-pro_1.0.0-${minor}_all.deb"
	mkdir -p ${pkgdir}/opt/amdgpu-pro/share
	mv "${srcdir}/libdrm/opt/amdgpu-pro/share/libdrm" "${pkgdir}/opt/amdgpu-pro/share"

	rm -r "${srcdir}/libdrm"
}


do_drm()
{
	mkdir -p "${srcdir}/libdrm"
	cd "${srcdir}/libdrm"

	unpack_deb "${srcdir}/${prefix}${major}-${minor}/libdrm-amdgpu-pro-amdgpu1_2.4.70-${minor}_amd64.deb"
	cd ${shared}

	rm "libdrm_amdgpu.so.1"
	mv "libdrm_amdgpu.so.1.0.0" "libdrm_amdgpo.so.1.0.0"
	ln -s "libdrm_amdgpo.so.1.0.0" "libdrm_amdgpo.so.1"

	mv "${srcdir}/opencl/etc" "${pkgdir}/"
	mkdir -p ${pkgdir}/usr/lib
	mv "${srcdir}/opencl/${shared}/libamdocl64.so" "${pkgdir}/usr/lib/"
	mv "${srcdir}/opencl/${shared}/libamdocl12cl64.so" "${pkgdir}/usr/lib/"
	mv "${srcdir}/libdrm/${shared}/libdrm_amdgpo.so.1.0.0" "${pkgdir}/usr/lib/"
	mv "${srcdir}/libdrm/${shared}/libdrm_amdgpo.so.1" "${pkgdir}/usr/lib/"

	rm -r "${srcdir}/libdrm"
}

package() {
    do_opencl

    do_drm
    do_drm_ids
}
