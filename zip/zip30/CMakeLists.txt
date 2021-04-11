project(zip)

cmake_minimum_required(VERSION 3.10)

if(COMMAND cmake_policy)
	cmake_policy(SET CMP0003 NEW)
endif(COMMAND cmake_policy)

include(darling_exe)
add_definitions(-DUNIX -DUNICODE_SUPPORT -DCRYPT)

# Hide warnings
add_definitions(
	-Wno-incompatible-pointer-types
)

set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -U_POSIX_C_SOURCE -D__DARWIN_UNIX03 -nostdinc -fblocks -fPIC -ggdb -O0")

include_directories(${CMAKE_CURRENT_SOURCE_DIR})

include(InstallSymlink)
set(BINARY_PACKAGING_MODE ON)

set(common_sources
	crc32.c
	crypt.c
	deflate.c
	fileio.c
	globals.c
	timezone.c
	trees.c
	ttyio.c
	util.c
	zbz2err.c
	zipfile.c
	unix/unix.c
	zipup.c
)
add_darling_object_library(zip_common ${common_sources})

set(commonutil_sources
	crc32.c
	fileio.c
	util.c
	zipfile.c
	ttyio.c
	unix/unix.c
	globals.c
)
add_darling_object_library(zip_commonutil ${commonutil_sources})
set_target_properties(zip_commonutil PROPERTIES COMPILE_FLAGS "-DUTIL")

add_darling_object_library(zip_commonutilcrypt crypt.c)
set_target_properties(zip_commonutilcrypt PROPERTIES COMPILE_FLAGS "-DUTIL")


set(zip_sources
	zip.c
	$<TARGET_OBJECTS:zip_common>
)

add_darling_executable(zip ${zip_sources})

set(zipcloak_sources
	zipcloak.c
	$<TARGET_OBJECTS:zip_commonutil>
	$<TARGET_OBJECTS:zip_commonutilcrypt>
)

add_darling_executable(zipcloak ${zipcloak_sources})

set(zipnote_sources
	zipnote.c
	$<TARGET_OBJECTS:zip_commonutil>
)

add_darling_executable(zipnote ${zipnote_sources})

set(zipsplit_sources
	zipsplit.c
	#zip.c
	$<TARGET_OBJECTS:zip_commonutil>
)

add_darling_executable(zipsplit ${zipsplit_sources})
#set_target_properties(zipsplit PROPERTIES COMPILE_FLAGS "-DUSE_ZIPMAIN")

install(TARGETS zip zipcloak zipnote zipsplit
	DESTINATION libexec/darling/usr/bin)

install(FILES
	man/zip.1
	man/zipcloak.1
	man/zipnote.1
	man/zipsplit.1

	DESTINATION libexec/darling/usr/share/man/man1
)
