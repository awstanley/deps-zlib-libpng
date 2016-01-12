# deps-zlib-libpng redist; CMakeLists.txt
#
# licence is public domain; if public domain isn't available to you
# (due to legalities/restrictions) feel free to consider this
# MIT licenced content.  The purpose of this document is to provide easy
# access to zlib and libpng; they are submodules within the git
# repository and minimal work is required to get a working opengl test
# up and running.  It can also be extended into full projects by adding
# more CMake based dependencies (e.g. glfw, glad, etc.).

# This is just what Visual Studio or whatever project manager you use
# will see the project as.  Changing this is meaningless.
PROJECT(deps_zlib_libpng C)

# I use a minimum of 3.0 flat for features anyway, though nothing here
# should require anything this high
CMAKE_MINIMUM_REQUIRED(VERSION 3.0 FATAL_ERROR)

# zlib/png integration hack - removes all the errors related to export
# (zlib doesn't export; libpng wants to; in this case we don't care).
SET(SKIP_INSTALL_EXPORT TRUE)

# If this is built standalone, and not part of an embedded project,
# define the build directory.
STRING(COMPARE EQUAL 
	${CMAKE_CURRENT_SOURCE_DIR}
	${CMAKE_SOURCE_DIR}
	IS_ROOT_PROJECT
)

IF(IS_ROOT_PROJECT)
	IF(NOT DEFINED DEPS_INSTALL_PREFIX)
		SET(DEPS_INSTALL_PREFIX
			"${CMAKE_BINARY_DIR}/build"
			CACHE STRING
			"Installation Prefix"
			FORCE
		)
	ENDIF()

	GET_PROPERTY(
		CMAKE_INSTALL_PREFIX_DOCS
		DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR}
		PROPERTY CMAKE_INSTALL_PREFIX
		FULL_DOCS
	)

	SET(CMAKE_INSTALL_PREFIX ${DEPS_INSTALL_PREFIX} CACHE STRING
		"${CMAKE_INSTALL_PREFIX_DOCS}" FORCE)

	SET(CMAKE_ARCHIVE_OUTPUT_DIRECTORY ${CMAKE_INSTALL_PREFIX}/bin)
	SET(CMAKE_LIBRARY_OUTPUT_DIRECTORY ${CMAKE_INSTALL_PREFIX}/bin)
	SET(CMAKE_RUNTIME_OUTPUT_DIRECTORY ${CMAKE_INSTALL_PREFIX}/bin)
	SET(CMAKE_PDB_OUTPUT_DIRECTORY ${CMAKE_INSTALL_PREFIX}/bin)
ENDIF()

# zlib (requires nothing)
SET(ZLIB_DIRECTORY "${CMAKE_CURRENT_SOURCE_DIR}/zlib")
ADD_SUBDIRECTORY(${ZLIB_DIRECTORY})

GET_DIRECTORY_PROPERTY(
	ZLIB_BUILD_DIRECTORY
	DIRECTORY
	${ZLIB_DIRECTORY}
	DEFINITION
	CMAKE_CURRENT_BINARY_DIR
)

OPTION(PNG_LINK_ZLIB_STATIC "Use a static zlib library for libpng builds" OFF)

# not needed, but paranoia for the future.
GET_DIRECTORY_PROPERTY(
	zlib
	DIRECTORY
	${ZLIB_DIRECTORY}
	DEFINITION
	zlib
)

# not needed, but paranoia for the future.
GET_DIRECTORY_PROPERTY(
	zlibstatic
	DIRECTORY
	${ZLIB_DIRECTORY}
	DEFINITION
	zlibstatic
)

IF(PNG_LINK_ZLIB_STATIC)
	SET(ZLIB_LIBRARY zlibstatic)
ELSE()
	SET(ZLIB_LIBRARY zlib)
ENDIF()
SET(ZLIB_INCLUDE_DIR ${ZLIB_DIRECTORY})

# libpng (requiring zlib)
SET(LIBPNG_DIRECTORY "${CMAKE_CURRENT_SOURCE_DIR}/libpng")

# libpng needs a hand.
INCLUDE_DIRECTORIES(
	${ZLIB_DIRECTORY}
	${ZLIB_BUILD_DIRECTORY}
	${LIBPNG_DIRECTORY}
	${LIBPNG_BUILD_DIRECTORY}
)

ADD_SUBDIRECTORY(${LIBPNG_DIRECTORY})

GET_DIRECTORY_PROPERTY(
	LIBPNG_BUILD_DIRECTORY
	DIRECTORY
	${LIBPNG_DIRECTORY}
	DEFINITION
	CMAKE_CURRENT_BINARY_DIR
)

GET_DIRECTORY_PROPERTY(
	LIBPNG_STATIC
	DIRECTORY
	${LIBPNG_DIRECTORY}
	DEFINITION
	PNG_LIB_NAME_STATIC
)

GET_DIRECTORY_PROPERTY(
	LIBPNG_SHARED
	DIRECTORY
	${LIBPNG_DIRECTORY}
	DEFINITION
	PNG_LIB_NAME
)


# Conditionally exported values
IF(NOT IS_ROOT_PROJECT)
	SET(ZLIB_DIRECTORY ${ZLIB_DIRECTORY} PARENT_SCOPE)
	SET(ZLIB_BUILD_DIRECTORY ${ZLIB_BUILD_DIRECTORY} PARENT_SCOPE)
	SET(ZLIB_STATIC zlibstatic PARENT_SCOPE)
	SET(ZLIB_SHARED zlib PARENT_SCOPE)
	SET(LIBPNG_DIRECTORY ${LIBPNG_DIRECTORY} PARENT_SCOPE)
	SET(LIBPNG_BUILD_DIRECTORY ${LIBPNG_BUILD_DIRECTORY} PARENT_SCOPE)
	SET(LIBPNG_STATIC ${LIBPNG_STATIC} PARENT_SCOPE)
	SET(LIBPNG_SHARED ${LIBPNG_SHARED} PARENT_SCOPE)

	# Setup the dependencies include directories, sending it upward for use.
	IF(NOT DEFINED DEPS_INCLUDE_DIRECTORIES)
		SET(DEPS_INCLUDE_DIRECTORIES
			${ZLIB_DIRECTORY}
			${ZLIB_BUILD_DIRECTORY}
			${LIBPNG_DIRECTORY}
			${LIBPNG_BUILD_DIRECTORY}
			PARENT_SCOPE
		)
	ELSE()
		# Already defined requires append semantics
		LIST(APPEND DEPS_INCLUDE_DIRECTORIES
			${ZLIB_DIRECTORY}
			${ZLIB_BUILD_DIRECTORY}
			${LIBPNG_DIRECTORY}
			${LIBPNG_BUILD_DIRECTORY}
		)
	ENDIF()
ENDIF()