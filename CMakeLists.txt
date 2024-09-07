CMAKE_MINIMUM_REQUIRED(VERSION 2.8.0)
PROJECT(NV)
ENABLE_TESTING()

SET(NV_CMAKE_DIR "${NV_SOURCE_DIR}/cmake")
SET(CMAKE_MODULE_PATH ${CMAKE_MODULE_PATH} "${NV_CMAKE_DIR}")


INCLUDE(${NV_CMAKE_DIR}/OptimalOptions.cmake)

IF(CMAKE_BUILD_TYPE MATCHES "debug")
    SET(CMAKE_DEBUG_POSTFIX "_d" CACHE STRING "Postfix for debug build libraries.")
    ADD_DEFINITIONS(-D_DEBUG=1)
ENDIF()


ADD_SUBDIRECTORY(extern)

ADD_SUBDIRECTORY(src)

INSTALL(
	TARGETS nvtt bc6h nvmath nvthread nvimage nvcore bc7 nvsquish rg_etc1 posh
  	EXPORT
		nvtt-targets
	RUNTIME
		DESTINATION bin
    LIBRARY
		DESTINATION lib
    ARCHIVE
		DESTINATION lib/static
)

install(
	EXPORT
		nvtt-targets
	NAMESPACE nvtt::
	DESTINATION
		cmake
)

# These files should only be installed when creating packages.
INSTALL(FILES
	LICENSE
	README.md
	DESTINATION share/doc/nvtt)


include(CMakePackageConfigHelpers)

configure_package_config_file(
	${CMAKE_CURRENT_SOURCE_DIR}/Config.cmake.in
  	${CMAKE_CURRENT_BINARY_DIR}/nvttConfig.cmake
  	INSTALL_DESTINATION
		cmake
)

install(
	FILES
   		${CMAKE_CURRENT_BINARY_DIR}/nvttConfig.cmake
    DESTINATION
		cmake
)

# Add packaging support
INCLUDE(InstallRequiredSystemLibraries)

IF(CMAKE_SYSTEM_NAME STREQUAL "Linux")
	SET(CPACK_GENERATOR "TGZ;DEB")
ENDIF(CMAKE_SYSTEM_NAME STREQUAL "Linux")

SET(CPACK_PACKAGE_NAME "nvidia-texture-tools")
SET(CPACK_PACKAGE_VERSION_MAJOR "2")
SET(CPACK_PACKAGE_VERSION_MINOR "1")
SET(CPACK_PACKAGE_VERSION_PATCH "2")
SET(CPACK_PACKAGE_VERSION "2.1.2")
SET(CPACK_PACKAGE_CONTACT "Ignacio Casta�o <castano@gmail.com>")
SET(CPACK_PACKAGE_DESCRIPTION_SUMMARY "Texture processing tools with support for Direct3D 10 and 11 formats.")

SET(CPACK_PACKAGE_DESCRIPTION_FILE "${NV_SOURCE_DIR}/README.md")
SET(CPACK_RESOURCE_FILE_LICENSE "${NV_SOURCE_DIR}/LICENSE")

# NSIS options:
IF(WIN32)
	SET(CPACK_NSIS_DISPLAY_NAME "${CPACK_PACKAGE_VENDOR}\\\\NVIDIA Texture Tools 2.1")
	SET(CPACK_PACKAGE_INSTALL_DIRECTORY "${CPACK_PACKAGE_VENDOR}\\\\NVIDIA Texture Tools 2.1")
	SET(CPACK_PACKAGE_ICON "${NV_SOURCE_DIR}\\\\project\\\\vc2017\\\\nvcompress\\\\nvidia.ico")
ENDIF(WIN32)

INCLUDE(CPack)
