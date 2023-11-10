cmake_minimum_required(VERSION 3.16.3)

project(qBreakpad C CXX ASM)

set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

set(CMAKE_AUTOMOC ON)

find_package(QT NAMES Qt6 Qt5 REQUIRED COMPONENTS Core Network)
find_package(Qt${QT_VERSION_MAJOR} REQUIRED COMPONENTS Core Network)
            
set(SOURCES
    handler/QBreakpadHandler.cpp
    handler/QBreakpadHttpUploader.cpp
)

set(HEADERS
    handler/singletone/call_once.h
    handler/singletone/singleton.h
    handler/QBreakpadHandler.h
    handler/QBreakpadHttpUploader.h
)

if (WIN32)
    list(APPEND SOURCES
        third_party/breakpad/src/client/windows/crash_generation/crash_generation_client.cc
        third_party/breakpad/src/client/windows/handler/exception_handler.cc
        third_party/breakpad/src/common/windows/guid_string.cc
    )
else()
    list(APPEND SOURCES
        third_party/breakpad/src/client/minidump_file_writer.cc
        third_party/breakpad/src/common/convert_UTF.cc
        third_party/breakpad/src/common/md5.cc
        third_party/breakpad/src/common/string_conversion.cc
        third_party/breakpad/src/client/linux/crash_generation/crash_generation_client.cc
        third_party/breakpad/src/client/linux/dump_writer_common/thread_info.cc
        third_party/breakpad/src/client/linux/dump_writer_common/ucontext_reader.cc
        third_party/breakpad/src/client/linux/handler/exception_handler.cc
        third_party/breakpad/src/client/linux/handler/minidump_descriptor.cc
        third_party/breakpad/src/client/linux/log/log.cc
        third_party/breakpad/src/client/linux/microdump_writer/microdump_writer.cc
        third_party/breakpad/src/client/linux/minidump_writer/linux_core_dumper.cc
        third_party/breakpad/src/client/linux/minidump_writer/linux_dumper.cc
        third_party/breakpad/src/client/linux/minidump_writer/linux_ptrace_dumper.cc
        third_party/breakpad/src/client/linux/minidump_writer/minidump_writer.cc
        third_party/breakpad/src/client/linux/minidump_writer/pe_file.cc
        third_party/breakpad/src/common/linux/elfutils.cc
        third_party/breakpad/src/common/linux/file_id.cc
        third_party/breakpad/src/common/linux/guid_creator.cc
        third_party/breakpad/src/common/linux/linux_libc_support.cc
        third_party/breakpad/src/common/linux/memory_mapped_file.cc
        third_party/breakpad/src/common/linux/safe_readlink.cc
        third_party/breakpad/src/common/linux/breakpad_getcontext.S
    )
endif()

if(${PROJECT_NAME} GREATER_EQUAL 6)
    qt_add_library(${PROJECT_NAME} STATIC
        MANUAL_FINALIZATION
        ${SOURCES} ${HEADERS}
    )
else()
    add_library(${PROJECT_NAME} STATIC
        ${SOURCES} ${HEADERS}
    )
endif()

target_include_directories(${PROJECT_NAME} PUBLIC
    ${CMAKE_CURRENT_SOURCE_DIR}
    handler
    third_party/breakpad/src
)

target_compile_definitions (${PROJECT_NAME} PRIVATE -D_UNICODE -DUNICODE)
target_link_libraries(${PROJECT_NAME} Qt${QT_VERSION_MAJOR}::Core Qt${QT_VERSION_MAJOR}::Network)
