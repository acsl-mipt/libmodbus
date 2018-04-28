project(libmodbus)

cmake_minimum_required(VERSION 2.8.11)

if(POLICY CMP0042)
    cmake_policy(SET CMP0042 NEW)
endif()

include(CheckIncludeFiles)
include(CheckFunctionExists)
include(CheckSymbolExists)

check_include_files(arpa/inet.h HAVE_ARPA_INET_H)
check_include_files(byteswap.h HAVE_BYTESWAP_H)
check_include_files(errno.h HAVE_ERRNO_H)
check_include_files(fcntl.h HAVE_FCNTL_H)
check_include_files(limits.h HAVE_LIMITS_H)
check_include_files(linux/serial.h HAVE_LINUX_SERIAL_H)
check_include_files(netdb.h HAVE_NETDB_H)
check_include_files(netinet/in.h HAVE_NETINET_IN_H)
check_include_files(netinet/tcp.h HAVE_NETINET_TCP_H)
check_include_files(sys/ioctl.h HAVE_SYS_IOCTL_H)
check_include_files(sys/socket.h HAVE_SYS_SOCKET_H)
check_include_files(sys/types.h HAVE_SYS_TYPES_H)
check_include_files(sys/time.h HAVE_SYS_TIME_H)
check_include_files(time.h HAVE_TIME_H)
check_include_files(termios.h HAVE_TERMIOS_H)
check_include_files(unistd.h HAVE_UNISTD_H)
check_include_files(winsock2.h HAVE_WINSOCK2_H)

check_function_exists(accept4 HAVE_ACCEPT4)
check_function_exists(geaddrinfo HAVE_GETADDRINFO)
check_function_exists(gettimeofday HAVE_GETTIMEOFDAY)
check_function_exists(inet_ntoa HAVE_INET_NTOA)
check_function_exists(memset HAVE_MEMSET)
check_function_exists(select HAVE_SELECT)
check_function_exists(socket HAVE_SOCKET)
check_function_exists(strerror HAVE_STRERROR)
check_function_exists(strlcpy HAVE_STRLCPY)

check_symbol_exists(__CYGWIN__ "" HAVE_DECL___CYGWIN__)
check_symbol_exists(TIOCSRS485 "sys/ioctl.h" HAVE_DECL_TIOCSRS485)
check_symbol_exists(TIOCM_RTS "sys/ioctl.h" HAVE_DECL_TIOCM_RTS)

if(USING_GCC)
    add_definitions(-D_GNU_SOURCE)
endif()

if(MSVC)
    add_definitions(/DDLLBUILD /D_CRT_SECURE_NO_DEPRECATE=1 /DCRT_NONSTDC_NO_DEPRECATE=1)
endif()

configure_file(${CMAKE_CURRENT_SOURCE_DIR}/cmake/config.h.in ${CMAKE_CURRENT_BINARY_DIR}/config.h)

include_directories(${CMAKE_CURRENT_BINARY_DIR})

add_library(modbus SHARED
    ${CMAKE_CURRENT_SOURCE_DIR}/src/modbus/modbus.h
    ${CMAKE_CURRENT_SOURCE_DIR}/src/modbus/modbus-private.h
    ${CMAKE_CURRENT_SOURCE_DIR}/src/modbus/modbus-rtu.h
    ${CMAKE_CURRENT_SOURCE_DIR}/src/modbus/modbus-rtu-private.h
    ${CMAKE_CURRENT_SOURCE_DIR}/src/modbus/modbus-tcp.h
    ${CMAKE_CURRENT_SOURCE_DIR}/src/modbus/modbus-tcp-private.h
    ${CMAKE_CURRENT_SOURCE_DIR}/src/modbus/modbus.c
    ${CMAKE_CURRENT_SOURCE_DIR}/src/modbus/modbus-data.c
    ${CMAKE_CURRENT_SOURCE_DIR}/src/modbus/modbus-rtu.c
    ${CMAKE_CURRENT_SOURCE_DIR}/src/modbus/modbus-tcp.c
)

if(HAVE_WINSOCK2_H)
    target_link_libraries(modbus ws2_32)
endif()

