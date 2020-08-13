cmake_minimum_required(VERSION 3.12...3.15)

project(cosemserver VERSION 1.0
                  DESCRIPTION "DLMS/Cosem server"
                  LANGUAGES C CXX)

set(TARGET cosemserver)

set(TOP_DIR ${PROJECT_SOURCE_DIR}/..)

add_executable(${TARGET})

target_include_directories(${TARGET} PRIVATE
    application
    system
    arch/host
    database
    ${TOP_DIR}/hdlc
    ${TOP_DIR}/src
    ${TOP_DIR}/share/crypto
    ${TOP_DIR}/share/ip
    ${TOP_DIR}/share/serial
    ${TOP_DIR}/share/util
    ${TOP_DIR}/cpp11-on-multicore/common
    ${TOP_DIR}/json
)

target_compile_definitions(${TARGET} PRIVATE
    COSEM_CLIENT_VER=\"1.2.0\"
    projCOVERAGE_TEST=0 
)

if(WIN32)
  target_compile_definitions(${TARGET} PRIVATE
    USE_WINDOWS_OS
  )
elseif(UNIX)
  target_compile_definitions(${TARGET} PRIVATE
    USE_UNIX_OS
  )
endif()

if(MSVC)
  target_compile_definitions(${TARGET} PRIVATE
    _CRT_SECURE_NO_WARNINGS
    _CRT_SECURE_NO_DEPRECATE
  )
elseif(MINGW)
  target_compile_definitions(${TARGET} PRIVATE
    STRSAFE_NO_DEPRECATE
  )
elseif(CMAKE_COMPILER_IS_GNUCXX)
  add_compile_options(
        -Wno-unused-function
        -Wno-unused-result
  )
endif()

target_sources( ${TARGET} PRIVATE
  database/db_cosem_clock.c
  database/db_cosem_associations.c
  database/db_cosem_image_transfer.c
  system/registry.c
  system/fs.c
  arch/host/bsp_flash.c
  application/app_calendar.c
  application/app_database.c
  src/cosem_server_hal.c
  src/main.c
  database/db_cosem_associations.h
  database/db_cosem_object_list.h
  database/db_cosem_image_transfer.h
  database/db_cosem_clock.h
  system/threads.h
  system/fs_config.h
  system/fs.h
  system/registry.h
  system/transports.h
  arch/host/bsp_flash.h
  application/app_definitions.h
  application/app_calendar.h
  application/app_database.h
  src/server_config.h
)

target_link_libraries(${TARGET} PRIVATE
    cosemlib
)

if(WIN32)
  target_link_libraries(${TARGET} PRIVATE
     ws2_32
  )    
endif()
