# Copyright (C) 2012 LuaDist
# Submitted by David Manura.
# Redistribution and use of this file is allowed according to the terms of the MIT license.
# For details see the COPYRIGHT file distributed with LuaDist.
# Please note that the package source code is licensed under its own license.

project ( jemalloc C )
cmake_minimum_required ( VERSION 2.6 )

message ( WARNING "TODO: this file is incomplete" )

#~TODO: configure.ac
set (install_suffix "" )
file ( READ "VERSION" jemalloc_version )
string ( STRIP "${jemalloc_version}" jemalloc_version )
string ( REGEX MATCH "([0-9]+)\\.([0-9]+)\\.([0-9]+)\\-([0-9]+)\\-([a-z0-9]+)" _out "${jemalloc_version}" )
set ( jemalloc_version_major ${CMAKE_MATCH_1} )
set ( jemalloc_version_minor ${CMAKE_MATCH_2} )
set ( jemalloc_version_bugfix ${CMAKE_MATCH_3} )
set ( jemalloc_version_nrev ${CMAKE_MATCH_4} )
set ( jemalloc_version_gid ${CMAKE_MATCH_5} )
configure_file ( include/jemalloc/internal/jemalloc_internal.h.in 
  ${CMAKE_CURRENT_BINARY_DIR}/jemalloc/internal/jemalloc_internal.h )
configure_file ( include/jemalloc/jemalloc.h.in 
  ${CMAKE_CURRENT_BINARY_DIR}/jemalloc/jemalloc.h )
message ( WARNING "TODO: defines not implemented" )
#JEMALLOC_DEFS_H_
#JEMALLOC_PREFIX
#JEMALLOC_CPREFIX
#JEMALLOC_PREFIX JEMALLOC_MANGLE   JEMALLOC_P
#JEMALLOC_PRIVATE_NAMESPACE
#JEMALLOC_N
#CPU_SPINWAIT
#JEMALLOC_OSATOMIC
#JEMALLOC_OSSPIN
#JEMALLOC_HAVE_ATTR
#JEMALLOC_HAVE_ATTR
#JEMALLOC_CC_SILENCE
#JEMALLOC_DEBUG
#JEMALLOC_STATS
#JEMALLOC_PROF
#JEMALLOC_PROF_LIBUNWIND
#JEMALLOC_PROF_LIBGCC
#JEMALLOC_PROF_GCC
#JEMALLOC_TINY
#JEMALLOC_TCACHE
#JEMALLOC_DSS
#JEMALLOC_SWAP
#JEMALLOC_FILL
#JEMALLOC_XMALLOC
#JEMALLOC_SYSV
#JEMALLOC_LAZY_LOCK
#DYNAMIC_PAGE_SHIFT
#STATIC_PAGE_SHIFT
#NO_TLS
#JEMALLOC_IVSALLOC
#JEMALLOC_OVERRIDE_MEMALIGN
#JEMALLOC_OVERRIDE_VALLOC
#JEMALLOC_ZONE
#JEMALLOC_ZONE_VERSION
#JEMALLOC_MREMAP_FIXED
#JEMALLOC_PURGE_MADVISE_DONTNEED
#JEMALLOC_PURGE_MADVISE_FREE
#LG_SIZEOF_PTR
#LG_SIZEOF_INT
#LG_SIZEOF_LONG
configure_file ( include/jemalloc/jemalloc_defs.h.in
  ${CMAKE_CURRENT_BINARY_DIR}/jemalloc/jemalloc_defs.h )

include_directories ( "${CMAKE_CURRENT_BINARY_DIR}" )
include_directories ( "include" )

set ( SRC
  src/jemalloc.c
  src/arena.c
  src/atomic.c
  src/base.c
  src/bitmap.c
  src/chunk.c
  src/chunk_dss.c
  src/chunk_mmap.c
  src/chunk_swap.c
  src/ckh.c
  src/ctl.c
  src/extent.c
  src/hash.c
  src/huge.c
  src/mb.c
  src/mutex.c
  src/prof.c
  src/rtree.c
  src/stats.c
  src/tcache.c
)
# if (macho STREQUAL @abi@)
#  src/zone.c
#endif ()

set ( TESTS
  allocated
  allocm
  bitmap
  mremap
  posix_memalign
  rallocm
  thread_arena
)

add_library ( jemalloc ${SRC} )

foreach ( test ${TESTS} )
  add_test ( ${test} test/${test}.c )
endforeach ()

install_library ( jemalloc )
install_data ( COPYING INSTALL README INTO jemalloc )
install_doc ( doc/ INTO jemalloc )
install_headers ( ${CMAKE_CURRENT_BINARY_DIR}/jemalloc/jemalloc.h INTO jemalloc )
install_headers ( ${CMAKE_CURRENT_BINARY_DIR}/jemalloc/jemalloc_defs.h INTO jemalloc )
