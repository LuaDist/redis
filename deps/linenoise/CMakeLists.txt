# Copyright (C) 2012 LuaDist
# Submitted by David Manura.
# Redistribution and use of this file is allowed according to the terms of the MIT license.
# For details see the COPYRIGHT file distributed with LuaDist.
# Please note that the package source code is licensed under its own license.

project ( llinenoise C )
cmake_minimum_required ( VERSION 2.6 )

add_library ( linenoise STATIC linenoise.c linenoise.h )

add_executable ( linenoise_example example.c  )
target_link_libraries ( linenoise_example linenoise )

install_data (
  README.markdown
  linenoise.c # contains license
  INTO linenoise
)

