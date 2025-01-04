#
# CMake configuration for open_jtalk
#
# adapted from https://gist.github.com/hkrn/889259/266e798f48a49d041d7f1e41a579d878134f1fca
cmake_minimum_required(VERSION 2.8.12...3.31)

# set library version
set(OPEN_JTALK_VERSION 1.11)
set(PACKAGE "open_jtalk")
set(PACKAGE_BUGREPORT "https://github.com/r9y9/open_jtalk/")
set(PACKAGE_NAME "open_jtalk")
set(PACKAGE_URL "")
set(PACKAGE_VERSION ${OPEN_JTALK_VERSION})
set(PACKAGE_STRING "${PACKAGE_NAME} ${PACKAGE_VERSION}")

# build configuration
project(openjtalk)

# whether build as a shared library or not
option(BUILD_SHARED_LIBS "Build Shared Libraries" OFF)

set(OPENJTALK_SOURCES)
include(jpcommon/CMakeLists.txt)
include(mecab2njd/CMakeLists.txt)
include(njd/CMakeLists.txt)
include(njd_set_accent_phrase/CMakeLists.txt)
include(njd_set_accent_type/CMakeLists.txt)
include(njd_set_digit/CMakeLists.txt)
include(njd_set_long_vowel/CMakeLists.txt)
include(njd_set_pronunciation/CMakeLists.txt)
include(njd_set_unvoiced_vowel/CMakeLists.txt)
include(njd2jpcommon/CMakeLists.txt)
include(text2mecab/CMakeLists.txt)
add_library(openjtalk ${OPENJTALK_SOURCES})
set_target_properties(openjtalk PROPERTIES VERSION ${OPEN_JTALK_VERSION})
target_include_directories(openjtalk PUBLIC
  "$<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/jpcommon>"
  "$<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/mecab2njd>"
  "$<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/njd>"
  "$<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/njd_set_accent_phrase>"
  "$<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/njd_set_accent_type>"
  "$<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/njd_set_digit>"
  "$<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/njd_set_long_vowel>"
  "$<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/njd_set_pronunciation>"
  "$<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/njd_set_unvoiced_vowel>"
  "$<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/njd2jpcommon>"
  "$<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/text2mecab>"
  $<INSTALL_INTERFACE:include>)

# configuration for charset
set(CHARSET utf8 CACHE STRING "Encoding set for mecab")
set_property(CACHE CHARSET PROPERTY STRINGS "utf8" "eucjp" "sjis")

add_subdirectory(mecab EXCLUDE_FROM_ALL)
target_link_libraries(openjtalk PUBLIC mecab)

add_subdirectory(mecab-naist-jdic)

if(CHARSET STREQUAL "sjis")
  target_compile_definitions(openjtalk PRIVATE CHARSET_SHIFT_JIS)
  target_compile_options(openjtalk PRIVATE
    $<$<C_COMPILER_ID:MSVC>:/source-charset:.932;/execution-charset:.932>
    $<$<NOT:$<C_COMPILER_ID:MSVC>>:-finput-charset=CP932;-fexec-charset=CP932>
  )
elseif(CHARSET STREQUAL "eucjp")
  target_compile_definitions(openjtalk PRIVATE CHARSET_EUC_JP)
  target_compile_options(openjtalk PRIVATE
    $<$<C_COMPILER_ID:MSVC>:/source-charset:euc-jp;/execution-charset:euc-jp>
    $<$<NOT:$<C_COMPILER_ID:MSVC>>:-finput-charset=EUC-JP;-fexec-charset=EUC-JP>
  )
elseif(CHARSET STREQUAL "utf8")
  target_compile_definitions(openjtalk PRIVATE CHARSET_UTF_8)
  target_compile_options(openjtalk PRIVATE
    $<$<C_COMPILER_ID:MSVC>:/utf-8>
    $<$<NOT:$<C_COMPILER_ID:MSVC>>:-finput-charset=UTF-8;-fexec-charset=UTF-8>
  )
else()
  message(FATAL_ERROR "Encoding ${CHARSET} not recognized. You can set sjis/eucjp/utf8")
endif()

option(BUILD_PROGRAMS "Build Programs" OFF)
if(BUILD_PROGRAMS)
  add_subdirectory(bin)
endif()

# installation
install(TARGETS openjtalk DESTINATION lib)
install(FILES jpcommon/jpcommon.h mecab/src/mecab.h mecab2njd/mecab2njd.h njd/njd.h njd2jpcommon/njd2jpcommon.h
        njd_set_accent_phrase/njd_set_accent_phrase.h njd_set_accent_type/njd_set_accent_type.h
        njd_set_digit/njd_set_digit.h njd_set_long_vowel/njd_set_long_vowel.h njd_set_pronunciation/njd_set_pronunciation.h
        njd_set_unvoiced_vowel/njd_set_unvoiced_vowel.h text2mecab/text2mecab.h
        DESTINATION include/openjtalk)
