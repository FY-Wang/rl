cmake_minimum_required(VERSION 2.8.11)

project(3rdparty)

include(ExternalProject)

if(WIN32)
	ExternalProject_Add(
		patch
		URL https://downloads.sourceforge.net/project/gnuwin32/patch/2.5.9-7/patch-2.5.9-7-bin.zip
		#URL ${CMAKE_CURRENT_SOURCE_DIR}/patch-2.5.9-7-bin.zip
		URL_MD5 b9c8b31d62f4b2e4f1887bbb63e8a905
		CONFIGURE_COMMAND ""
		BUILD_COMMAND ""
		INSTALL_COMMAND
		${CMAKE_COMMAND} -E copy <SOURCE_DIR>/bin/patch.exe ${CMAKE_CURRENT_BINARY_DIR}/patch.exe &&
		${CMAKE_COMMAND} -E copy ${CMAKE_CURRENT_SOURCE_DIR}/patch.exe.manifest ${CMAKE_CURRENT_BINARY_DIR}/patch.exe.manifest
	)
	set(PATCH_EXECUTABLE ${CMAKE_CURRENT_BINARY_DIR}/patch.exe)
else()
	ExternalProject_Add(
		patch
		SOURCE_DIR ${CMAKE_CURRENT_SOURCE_DIR}
		CONFIGURE_COMMAND ""
		BUILD_COMMAND ""
		INSTALL_COMMAND ""
	)
	set(PATCH_EXECUTABLE patch)
endif()

add_subdirectory(atidaq)
add_subdirectory(boost)
add_subdirectory(bullet)
add_subdirectory(eigen)
add_subdirectory(libiconv)
add_subdirectory(nlopt)
add_subdirectory(ode)
add_subdirectory(pqp)
add_subdirectory(simage)
add_subdirectory(solid)
add_subdirectory(zlib)

add_subdirectory(libxml2)
add_subdirectory(coin)

add_subdirectory(libxslt)
add_subdirectory(soqt)

set(
	CPACK_INSTALL_CMAKE_PROJECTS
	"${CMAKE_CURRENT_BINARY_DIR}/atidaq/atidaq-prefix/src/atidaq-build;atidaq;ALL;/"
	"${CMAKE_CURRENT_BINARY_DIR}/bullet/bullet-prefix/src/bullet-build;bullet;ALL;/"
	"${CMAKE_CURRENT_BINARY_DIR}/coin/coin-prefix/src/coin-build;coin;ALL;/"
	"${CMAKE_CURRENT_BINARY_DIR}/eigen/eigen-prefix/src/eigen-build;eigen;ALL;/"
	"${CMAKE_CURRENT_BINARY_DIR}/libiconv/libiconv-prefix/src/libiconv-build;libiconv;ALL;/"
	"${CMAKE_CURRENT_BINARY_DIR}/libxml2/libxml2-prefix/src/libxml2-build;libxml2;ALL;/"
	"${CMAKE_CURRENT_BINARY_DIR}/libxslt/libxslt-prefix/src/libxslt-build;libxslt;ALL;/"
	"${CMAKE_CURRENT_BINARY_DIR}/nlopt/nlopt-prefix/src/nlopt-build;nlopt;ALL;/"
	"${CMAKE_CURRENT_BINARY_DIR}/ode/ode-prefix/src/ode-build;ode;ALL;/"
	"${CMAKE_CURRENT_BINARY_DIR}/pqp/pqp-prefix/src/pqp-build;pqp;ALL;/"
	"${CMAKE_CURRENT_BINARY_DIR}/simage/simage-prefix/src/simage-build;simage;ALL;/"
	"${CMAKE_CURRENT_BINARY_DIR}/solid/solid-prefix/src/solid-build;solid;ALL;/"
	"${CMAKE_CURRENT_BINARY_DIR}/soqt/soqt-prefix/src/soqt-build;soqt;ALL;/"
	"${CMAKE_CURRENT_BINARY_DIR}/zlib/zlib-prefix/src/zlib-build;zlib;ALL;/"
)

set(
	CPACK_INSTALLED_DIRECTORIES
	${CMAKE_CURRENT_BINARY_DIR}/boost/boost-prefix/src/boost/boost ${CMAKE_INSTALL_INCLUDEDIR}/boost
)

set(CPACK_PACKAGE_VERSION_MAJOR 0)
set(CPACK_PACKAGE_VERSION_MINOR 7)
set(CPACK_PACKAGE_VERSION_PATCH 0)

set(CPACK_PACKAGE_VERSION ${CPACK_PACKAGE_VERSION_MAJOR}.${CPACK_PACKAGE_VERSION_MINOR}.${CPACK_PACKAGE_VERSION_PATCH})

set(CPACK_DEBIAN_PACKAGE_HOMEPAGE "https://www.roboticslibrary.org/contact")
set(CPACK_DEBIAN_PACKAGE_NAME "librl-3rdparty")
set(CPACK_DEBIAN_PACKAGE_SECTION "devel")
set(CPACK_NSIS_DISPLAY_NAME "Robotics Library ${CPACK_PACKAGE_VERSION} Third-Party Dependencies")
set(CPACK_NSIS_EXECUTABLES_DIRECTORY ${CMAKE_INSTALL_BINDIR})
set(CPACK_NSIS_HELP_LINK "https://www.roboticslibrary.org/contact")
set(CPACK_NSIS_INSTALLED_ICON_NAME Uninstall.exe)
set(CPACK_NSIS_MODIFY_PATH ON)
set(CPACK_NSIS_MUI_ICON ${CMAKE_CURRENT_SOURCE_DIR}/../robotics-library.ico)
set(CPACK_NSIS_MUI_UNIICON ${CMAKE_CURRENT_SOURCE_DIR}/../robotics-library.ico)
set(CPACK_NSIS_PACKAGE_NAME "Robotics Library ${CPACK_PACKAGE_VERSION} Third-Party Dependencies")
set(CPACK_NSIS_URL_INFO_ABOUT "https://www.roboticslibrary.org/contact")
set(CPACK_PACKAGE_CONTACT "Robotics Library Team <roblib-developers@lists.sourceforge.net>")
set(CPACK_PACKAGE_DISPLAY_NAME "Robotics Library ${CPACK_PACKAGE_VERSION} Third-Party Dependencies")
set(CPACK_PACKAGE_NAME "rl-3rdparty")
set(CPACK_PACKAGE_INSTALL_DIRECTORY "rl-${CPACK_PACKAGE_VERSION}")
set(CPACK_PACKAGE_VENDOR "")
set(CPACK_RPM_PACKAGE_GROUP "Development/Libraries")
set(CPACK_RPM_PACKAGE_NAME "rl-3rdparty")
set(CPACK_RPM_PACKAGE_SUMMARY "Robotics Library Third-Party Dependencies")
set(CPACK_RPM_PACKAGE_URL "https://www.roboticslibrary.org/contact")

include(CPack)
