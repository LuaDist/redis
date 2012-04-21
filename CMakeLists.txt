# Copyright (C) 2012 LuaDist
# Submitted by David Manura.
# Redistribution and use of this file is allowed according to the terms of the MIT license.
# For details see the COPYRIGHT file distributed with LuaDist.
# Please note that the package source code is licensed under its own license.

project ( redis C )
cmake_minimum_required ( VERSION 2.6 )
include ( cmake/dist.cmake )

add_subdirectory ( deps/hiredis )
add_subdirectory ( deps/linenoise )

if ( 0 ) # FIX: sunos
  set ( CCLINK -ldl -lnsl -lsocket -lm -lpthread )
else ()
  set ( CCLINK -lm -pthread )
endif ()

if ( CMAKE_SYSTEM_NAME STREQUAL "Linux" )

  message ( WARNING "WARNING: enabling FORCE_LIBC_MALLOC since jemalloc build is incomplete at this time" )
  set ( FORCE_LIBC_MALLOC 1 )
  
  if ( NOT FORCE_LIBC_MALLOC )
    set ( USE_JEMALLOC 1 )
  endif ()
endif ()

if ( USE_TCMALLOC )
  set ( ALLOC_LINK -ltcmalloc )
  set ( ALLOC_FLAGS -DUSE_TCMALLOC )
elseif ( USE_TCMALLOC_MINIMAL )
  set ( ALLOC_LINK -ltcmalloc_minimal )
  set ( ALLOC_FLAGS -DUSE_TCMALLOC )
elseif ( USE_JEMALLOC )
  add_subdirectory ( deps/jemalloc )
  set ( ALLOC_DEP jemalloc )
  set ( ALLOC_LINK ${ALLOC_DEP} -ldl )
  set ( ALLOC_FLAGS -DUSE_JEMALLOC )
  include_directories ( deps/jemalloc/include )
endif ()

set ( SRC
  src/adlist.c
  src/ae.c
  src/anet.c
  src/dict.c
  src/redis.c
  src/sds.c
  src/zmalloc.c
  src/lzf_c.c
  src/lzf_d.c
  src/pqsort.c
  src/zipmap.c
  src/sha1.c
  src/ziplist.c
  src/release.c
  src/networking.c
  src/util.c
  src/object.c
  src/db.c
  src/replication.c
  src/rdb.c
  src/t_string.c
  src/t_list.c
  src/t_set.c
  src/t_zset.c
  src/t_hash.c
  src/config.c
  src/aof.c
  src/vm.c
  src/pubsub.c
  src/multi.c
  src/debug.c
  src/sort.c
  src/intset.c
  src/syncio.c
  src/slowlog.c
  src/bio.c
  src/memtest.c
)
set ( BENCHSRC src/ae.c src/anet.c src/redis-benchmark.c
  src/sds.c src/adlist.c src/zmalloc.c )
set ( CLISRC src/anet.c src/sds.c src/adlist.c src/redis-cli.c
  src/zmalloc.c src/release.c )
set ( CHECKDUMPSRC src/redis-check-dump.c src/lzf_c.c src/lzf_d.c )
set ( CHECKAOFSRC src/redis-check-aof.c )

add_executable ( redis-benchmark ${BENCHSRC} )
target_link_libraries ( redis-benchmark hiredis ${CCLINK} ${ALLOC_LINK} )

add_executable ( redis-check-dump ${CHECKDUMPSRC} )
target_link_libraries ( redis-check-dump ${CCLINK} )

add_executable ( redis-check-aof ${CHECKAOFSRC} )
target_link_libraries ( redis-check-aof ${CCLINK} )

add_executable ( redis-server ${SRC} )
target_link_libraries ( redis-server ${CCLINK} ${ALLOC_LINK} )

include_directories ( deps/hiredis deps/linenoise  )
add_executable ( redis-cli ${CLISRC} )
target_link_libraries ( redis-cli hiredis linenoise ${CCLINK} ${ALLOC_LINK} )

# warning, tests depend on tcl.  possibly could rewrite without tcl.
enable_testing()
find_program ( TCL tclsh8.5 )
if ( NOT TCL )
  message ( WARNING "WARNING: tcl not found.  tests will be skipped" )
elseif ( UNIX )
  execute_process ( COMMAND ${CMAKE_COMMAND} -E create_symlink
    ${CMAKE_CURRENT_BINARY_DIR} ${CMAKE_CURRENT_BINARY_DIR}/src )
  execute_process ( COMMAND ${CMAKE_COMMAND} -E create_symlink
    ${CMAKE_CURRENT_SOURCE_DIR}/tests ${CMAKE_CURRENT_BINARY_DIR}/tests )
  add_test ( NAME runtest WORKING_DIRECTORY ${CMAKE_CURRENT_BINARY_DIR}
     COMMAND ${CMAKE_CURRENT_SOURCE_DIR}/runtest
     )
else ()
  message ( WARNING "WARNING: create_symlink is *nix only.  tests will fail" )
endif ()

install_executable ( redis-server redis-cli redis-check-aof redis-check-dump redis-benchmark )
install_data ( 00-RELEASENOTES BUGS CONTRIBUTING COPYING INSTALL README )
