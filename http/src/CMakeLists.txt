# Copyright (c) Glyn Matthews 2011, 2012, 2013.
# Copyright 2011 Dean Michael Berris (dberris@google.com)
# Copyright 2012 A. Joel Lamotte (mjklaim@gmail.com)
# Copyright 2011 Google, Inc.
# Distributed under the Boost Software License, Version 1.0.
# (See accompanying file LICENSE_1_0.txt or copy at
# http://www.boost.org/LICENSE_1_0.txt)


include_directories(
  ${CPP-NETLIB_SOURCE_DIR}/config/src
  ${CPP-NETLIB_SOURCE_DIR}/uri/src
  ${CPP-NETLIB_SOURCE_DIR}/message/src
  ${CPP-NETLIB_SOURCE_DIR}/logging/src
  ${CPP-NETLIB_SOURCE_DIR}/http/src
  ${CPP-NETLIB_SOURCE_DIR})

if (OPENSSL_FOUND)
include_directories(${OPENSSL_INCLUDE_DIR})
endif()

set(CPP-NETLIB_HTTP_MESSAGE_SRCS
  http/request.cpp
  http/response.cpp)

if(NOT CPP-NETLIB_BUILD_SINGLE_LIB)
  add_library(network-http-message ${CPP-NETLIB_HTTP_MESSAGE_SRCS})
  target_link_libraries(network-http-message
    ${Boost_LIBRARIES}
    network-message)
  target_include_directories(network-http-message PUBLIC ${Boost_INCLUDE_DIRS})
endif()

set(CPP-NETLIB_HTTP_MESSAGE_WRAPPERS_SRCS
    http/message/wrappers.cpp)

if(NOT CPP-NETLIB_BUILD_SINGLE_LIB)
  add_library(network-http-message-wrappers ${CPP-NETLIB_HTTP_MESSAGE_WRAPPERS_SRCS})
  target_include_directories(network-http-message-wrappers PUBLIC ${Boost_INCLUDE_DIRS})
endif()

set(CPP-NETLIB_CONSTANTS_SRCS
    constants.cpp)

if(NOT CPP-NETLIB_BUILD_SINGLE_LIB)
  add_library(network-constants ${CPP-NETLIB_CONSTANTS_SRCS})
  target_include_directories(network-constants PUBLIC ${Boost_INCLUDE_DIRS})
endif()

# Server implementation files.
set(CPP-NETLIB_HTTP_SERVER_SRCS
  http/server/session.cpp
  http/server/simple_sessions.cpp
  http/server/dynamic_dispatcher.cpp)

if (NOT CPP-NETLIB_BUILD_SINGLE_LIB)
  add_library(network-http-server ${CPP-NETLIB_HTTP_SERVER_SRCS})
  target_include_directories(network-http-server PUBLIC ${Boost_INCLUDE_DIRS})
endif()

# HTTP client
set(CPP-NETLIB_HTTP_V2_CLIENT_SRCS
  ${CMAKE_CURRENT_SOURCE_DIR}/http/v2/client/client.cpp
  ${CMAKE_CURRENT_SOURCE_DIR}/http/v2/client/client_errors.cpp
  )
add_library(network-http-v2-client ${CPP-NETLIB_HTTP_V2_CLIENT_SRCS})
target_link_libraries(network-http-v2-client
  ${Boost_LIBRARIES}
  network-uri
  )
target_include_directories(network-http-v2-client PUBLIC ${Boost_INCLUDE_DIRS})
if (OPENSSL_FOUND)
  target_link_libraries(network-http-v2-client ${OPENSSL_LIBRARIES})
endif()

# prepend current directory to make paths absolute
prependToElements( "${CMAKE_CURRENT_SOURCE_DIR}/"
    CPP-NETLIB_HTTP_MESSAGE_SRCS
    CPP-NETLIB_HTTP_MESSAGE_WRAPPERS_SRCS
    CPP-NETLIB_CONSTANTS_SRCS
    CPP-NETLIB_HTTP_SERVER_SRCS
    CPP-NETLIB_HTTP_V2_CLIENT_SRCS )

# propagate sources to parent directory for one-lib-build
set(CPP-NETLIB_HTTP_MESSAGE_SRCS ${CPP-NETLIB_HTTP_MESSAGE_SRCS} PARENT_SCOPE)
set(CPP-NETLIB_HTTP_MESSAGE_WRAPPERS_SRCS ${CPP-NETLIB_HTTP_MESSAGE_WRAPPERS_SRCS} PARENT_SCOPE)
set(CPP-NETLIB_HTTP_SERVER_SRCS ${CPP-NETLIB_HTTP_SERVER_SRCS} PARENT_SCOPE)
set(CPP-NETLIB_CONSTANTS_SRCS ${CPP-NETLIB_CONSTANTS_SRCS} PARENT_SCOPE)
set(CPP-NETLIB_HTTP_V2_CLIENT_SRCS ${CPP-NETLIB_HTTP_V2_CLIENT_SRCS} PARENT SCOPE)
