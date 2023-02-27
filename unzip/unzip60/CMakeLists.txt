project(unzip)

cmake_minimum_required(VERSION 3.10)

if(COMMAND cmake_policy)
	cmake_policy(SET CMP0003 NEW)
endif(COMMAND cmake_policy)

include(darling_exe)
add_definitions(-DUNIX -DUNICODE_SUPPORT -DCRYPT -DLARGE_FILE_SUPPORT -DBSD)

# Hide warnings
add_definitions(
	-Wno-format-security
	-Wno-pointer-sign
	-Wno-incompatible-pointer-types
	-Wno-format-extra-args
)

set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -U_POSIX_C_SOURCE -D__DARWIN_UNIX03 -nostdinc -fblocks -fPIC -ggdb -O0")

include_directories(${CMAKE_CURRENT_SOURCE_DIR})

include(InstallSymlink)
set(BINARY_PACKAGING_MODE ON)

set(unzip_sources
	crc32.c
	crypt.c
	envargs.c
	explode.c
	extract.c
	fileio.c
	globals.c
	inflate.c
	list.c
	match.c
	process.c
	ttyio.c
	ubz2err.c
	unix/unix.c
	unreduce.c
	unshrink.c
	unzip.c
	zipinfo.c
)

add_darling_executable(unzip ${unzip_sources})
target_link_libraries(unzip z)

set(funzip_sources
	funzip.c
	crc32.c
	crypt.c
	globals.c
	inflate.c
	ttyio.c
)

add_darling_executable(funzip ${funzip_sources})
target_link_libraries(funzip z)
set_target_properties(funzip PROPERTIES COMPILE_FLAGS "-DFUNZIP")

set(unzipsfx_sources
	unzip.c
	crc32.c
	crypt.c
	extract.c
	fileio.c
	globals.c
	inflate.c
	match.c
	process.c
	ttyio.c
	ubz2err.c
	unix/unix.c
)

add_darling_executable(unzipsfx ${unzipsfx_sources})
target_link_libraries(unzipsfx z)
set_target_properties(unzipsfx PROPERTIES COMPILE_FLAGS "-DSFX")

InstallSymlink("unzip" "${CMAKE_INSTALL_PREFIX}/libexec/darling/usr/bin/zipinfo")
install(TARGETS unzip funzip unzipsfx
	DESTINATION libexec/darling/usr/bin)
install(PROGRAMS unix/zipgrep
	DESTINATION libexec/darling/usr/bin)

install(FILES
	man/funzip.1
	man/unzip.1
	man/unzipsfx.1
	man/zipgrep.1
	man/zipinfo.1

	DESTINATION libexec/darling/usr/share/man/man1
)
