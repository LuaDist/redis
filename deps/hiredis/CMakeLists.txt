# Copyright (C) 2012 LuaDist
# Submitted by David Manura.
# Redistribution and use of this file is allowed according to the terms of the MIT license.
# For details see the COPYRIGHT file distributed with LuaDist.
# Please note that the package source code is licensed under its own license.

project ( hiredis C )
cmake_minimum_required ( VERSION 2.6 )

set ( AE_DIR ${CMAKE_CURRENT_SOURCE_DIR}/../../src )

if ( 0 ) #2DO: sunos
  set ( REAL_LDFLAGS -ldl -lnsl -lsocket )
endif ()

set ( SRC net.c hiredis.c sds.c async.c )

add_library ( hiredis ${SRC} )

# requires event.h
#add_executable ( hiredis-example-libevent example-libevent.c adapters/libevent.h )
#target_link_libraries ( hiredis-example-libevent hiredis -levent )

# requires ev.h
#add_executable ( hiredis-example-libev example-libev.c adapters/libev.h )
#target_link_libraries ( hiredis-example-libev hiredis -lev )

#include_directories ( ${AE_DIR} )
#add_executable ( hiredis-example-ae example-ae.c adapters/ae.h
#  ${AE_DIR}/ae.c ${AE_DIR}/zmalloc.c
#)
#target_link_libraries ( hiredis-example-ae hiredis )

install_header ( hiredis.h async.h adapters )
install_library ( hiredis )
install_data ( CHANGELOG.md COPYING README.md INTO hiredis )
