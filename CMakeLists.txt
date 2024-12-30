cmake_minimum_required(VERSION 3.13)

set(PROGRAM_NAME picow_mdns_webserver)
set(PICO_BOARD pico_w)

if(NOT $ENV{PICO_SDK_PATH})
#  set(PICO_SDK_PATH ${CMAKE_CURRENT_LIST_DIR}/lib/pico-sdk)
   message(" you must have pico sdk and environment PICO_SDK_PATH defined")
endif()
set(PICO_SDK_PATH $ENV{PICO_SDK_PATH})

message(PICO_SDK_PATH="${PICO_SDK_PATH}")

include(pico_sdk_import.cmake)

project(${PROGRAM_NAME})

pico_sdk_init()

message("Running makefsdata python script")
execute_process(COMMAND
    python3 makefsdata.py
    WORKING_DIRECTORY ${CMAKE_CURRENT_LIST_DIR}
)

add_executable(${PROGRAM_NAME}
    main.c
    ${PICO_SDK_PATH}/lib/lwip/src/apps/mdns/mdns.c
    ${PICO_SDK_PATH}/lib/lwip/src/apps/mdns/mdns_domain.c
    ${PICO_SDK_PATH}/lib/lwip/src/apps/mdns/mdns_out.c
    mdns_example.c
)
pico_set_linker_script(${PROGRAM_NAME} ${CMAKE_SOURCE_DIR}/memmap_custom.ld)
target_compile_options(${PROGRAM_NAME} PUBLIC -Werror -Wall -Wextra -Wnull-dereference -Wno-unused-parameter -Wno-null-dereference -ggdb3 -O3)
string(APPEND CMAKE_EXE_LINKER_FLAGS "-Wl,--print-memory-usage")

target_include_directories(${PROGRAM_NAME} PRIVATE
    ${CMAKE_CURRENT_LIST_DIR}
)
#pass some cmake variables to the program, warning some may be case sensitive.
target_compile_definitions(${PROGRAM_NAME} PRIVATE
    WIFI_SSID=\"${WIFI_SSID}\"
    WIFI_PASSWORD=\"${WIFI_PASSWORD}\"
    HOSTNAME=\"${HOSTNAME}\"
    )

target_link_libraries(${PROGRAM_NAME}
    pico_cyw43_arch_lwip_threadsafe_background
    pico_lwip_http
    pico_stdlib
    hardware_adc
    pico_rand
)

pico_enable_stdio_usb(${PROGRAM_NAME} TRUE)
pico_enable_stdio_uart(${PROGRAM_NAME} FALSE)

pico_add_extra_outputs(${PROGRAM_NAME})