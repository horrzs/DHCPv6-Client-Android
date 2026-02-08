cmake_minimum_required(VERSION 3.4.1)

set(ROOT src/main/cpp/wide-dhcpv6)

# --- Executables (方案A) ---
add_executable(dhcp6c
    src/main/cpp/ifaddrs.cpp
    ${ROOT}/addrconf.c
    ${ROOT}/auth.c
    ${ROOT}/base64.c
    ${ROOT}/cfparse.c
    ${ROOT}/cftoken.c
    ${ROOT}/common.c
    ${ROOT}/config.c
    ${ROOT}/dhcp6_ctl.c
    ${ROOT}/dhcp6c.c
    ${ROOT}/dhcp6c_ia.c
    ${ROOT}/dhcp6c_script.c
    ${ROOT}/if.c
    ${ROOT}/lease.c
    ${ROOT}/prefixconf.c
    ${ROOT}/timer.c
)

add_executable(dhcp6ctl
    ${ROOT}/auth.c
    ${ROOT}/base64.c
    ${ROOT}/dhcp6_ctlclient.c
)

target_include_directories(dhcp6c PRIVATE ${ROOT})
target_include_directories(dhcp6ctl PRIVATE ${ROOT})

# --- Compile definitions (原来的 add_definitions 改成 target 级别更干净) ---
target_compile_definitions(dhcp6c PRIVATE
    SYSCONFDIR="."
    LOCALDBDIR="."
    LINE_MAX=120
    HAVE_GETADDRINFO=1
    HAVE_GETNAMEINFO=1
    HAVE_IF_NAMETOINDEX=1
    HAVE_STRLCPY=1
    HAVE_STRLCAT=1
    HAVE_DAEMON=1
    HAVE_WARNX=1
    STDC_HEADERS=1
    HAVE_SYS_TYPES_H=1
    HAVE_SYS_STAT_H=1
    HAVE_STDLIB_H=1
    HAVE_STRING_H=1
    HAVE_MEMORY_H=1
    HAVE_STRINGS_H=1
    HAVE_INTTYPES_H=1
    HAVE_STDINT_H=1
    HAVE_UNISTD_H=1
    HAVE_FCNTL_H=1
    HAVE_SYS_IOCTL_H=1
    HAVE_SYS_TIME_H=1
    HAVE_SYSLOG_H=1
    HAVE_IFADDRS_H=1
    TIME_WITH_SYS_TIME=1
    HAVE_STRUCT_TM_TM_ZONE=1
    HAVE_TM_ZONE=1
    HAVE_SIG_ATOMIC_T=1
    GETPGRP_VOID=1
    SETPGRP_VOID=1
    RETSIGTYPE=void
    HAVE_MKTIME=1
    HAVE_SELECT=1
    HAVE_SOCKET=1
    HAVE_CLOCK_GETTIME=1
    HAVE_ARC4RANDOM=1
    HAVE_ANSI_FUNC=1
    HAVE_TAILQ_FOREACH_REVERSE_OLD=1
    HAVE_STDARG_H=1
)

# dhcp6ctl 也需要这些宏（至少 SYSCONFDIR/LOCALDBDIR/LINE_MAX + headers 宏）
target_compile_definitions(dhcp6ctl PRIVATE
    SYSCONFDIR="."
    LOCALDBDIR="."
    LINE_MAX=120
    HAVE_STDARG_H=1
    STDC_HEADERS=1
)

# --- Compile options ---
target_compile_options(dhcp6c PRIVATE -Wno-format-zero-length)
target_compile_options(dhcp6ctl PRIVATE -Wno-format-zero-length)

# 推荐：明确 C/C++ 标准（避免更激进的默认行为）
set_target_properties(dhcp6c PROPERTIES
    C_STANDARD 99
    C_STANDARD_REQUIRED YES
    CXX_STANDARD 17
    CXX_STANDARD_REQUIRED YES
)
set_target_properties(dhcp6ctl PROPERTIES
    C_STANDARD 99
    C_STANDARD_REQUIRED YES
)

# --- Link libs (Android 常见需要) ---
find_library(log-lib log)
find_library(android-lib android)
find_library(m-lib m)

target_link_libraries(dhcp6c PRIVATE ${log-lib} ${android-lib} ${m-lib})
target_link_libraries(dhcp6ctl PRIVATE ${log-lib} ${android-lib} ${m-lib})
