# Maintainer: K_log Televised - youtube klog.website.notdotcom@gmail.com
pkgname=cpp-mcp
pkgver=alpha
pkgrel=1
pkgdesc="C++ implementation of Model Context Protocol (MCP)"
arch=('x86_64' 'i686' 'armv7h' 'aarch64')
url="https://github.com/klogdotwebsitenotdotcom/cpp-mcp"
license=('MIT')
depends=('openssl')
makedepends=('cmake' 'ninja' 'git')
# Use git source with specific branch
source=("git+${url}.git#branch=distribution")
sha256sums=('SKIP')

# Build options
options=('!strip' 'staticlibs')
: ${MCP_SSL:=OFF}

build() {
  cd "$srcdir/cpp-mcp"
  
  cmake -B build \
    -DCMAKE_BUILD_TYPE=Release \
    -DMCP_SSL=${MCP_SSL} \
    -DCMAKE_INSTALL_PREFIX=/usr
  
  cmake --build build
}

check() {
  cd "$srcdir/cpp-mcp/build"
  ctest --output-on-failure
}

package() {
  cd "$srcdir/cpp-mcp"
  
  DESTDIR="$pkgdir" cmake --install build
  
  # Move library to cpp-mcp namespace
  if [ -f "$pkgdir/usr/lib/libmcp.a" ]; then
    mv "$pkgdir/usr/lib/libmcp.a" "$pkgdir/usr/lib/libcpp-mcp.a"
  fi
  
  # Move headers to cpp-mcp namespace
  if [ -d "$pkgdir/usr/include" ]; then
    mkdir -p "$pkgdir/usr/include/cpp-mcp"
    mv "$pkgdir/usr/include/mcp_"*.h "$pkgdir/usr/include/cpp-mcp/" 2>/dev/null || true
    mv "$pkgdir/usr/include/mcp" "$pkgdir/usr/include/cpp-mcp/" 2>/dev/null || true
  fi
  
  # Install license
  install -Dm644 LICENSE "$pkgdir/usr/share/licenses/$pkgname/LICENSE"
  
  # Install README and other documentation
  install -Dm644 README.md "$pkgdir/usr/share/doc/$pkgname/README.md"
  
  # Create pkg-config file for easy linking
  install -Dm644 /dev/stdin "$pkgdir/usr/lib/pkgconfig/cpp-mcp.pc" <<EOF
prefix=/usr
exec_prefix=\${prefix}
libdir=\${prefix}/lib
includedir=\${prefix}/include/cpp-mcp

Name: cpp-mcp
Description: C++ implementation of Model Context Protocol (MCP)
Version: ${pkgver}
Libs: -L\${libdir} -lcpp-mcp -lpthread
Cflags: -I\${includedir}
Requires: openssl
EOF
} 
