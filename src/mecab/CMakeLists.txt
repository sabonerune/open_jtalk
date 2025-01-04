# generate mecab/config.h
include(CheckIncludeFiles)
include(CheckFunctionExists)
check_include_files(dirent.h HAVE_DIRENT_H)
check_include_files(fcntl.h HAVE_FCNTL_H)
check_include_files(stdint.h HAVE_STDINT_H)
check_include_files(string.h HAVE_STRING_H)
check_include_files(sys/mman.h HAVE_SYS_MMAN_H)
check_include_files(sys/stat.h HAVE_SYS_STAT_H)
check_include_files(sys/types.h HAVE_SYS_TYPES_H)
check_include_files(unistd.h HAVE_UNISTD_H)
check_include_files(windows.h HAVE_WINDOWS_H)
check_function_exists(mmap HAVE_MMAP)
set(VERSION \"${OPEN_JTALK_VERSION}\")

if(NOT CHARSET STREQUAL utf8)
  if(CMAKE_VERSION VERSION_LESS 3.11)
    include(CheckSymbolExists)
    check_symbol_exists(iconv_open iconv.h HAVE_ICONV)
  else()
    find_package(Iconv)
    if(Iconv_FOUND)
      set(HAVE_ICONV 1)
    endif()
  endif()
  if(NOT HAVE_ICONV AND NOT HAVE_WINDOWS_H)
    message(FATAL_ERROR "Could not encord to ${CHARSET}.")
  endif()
endif()
if(HAVE_ICONV)
  include(CheckCXXSourceCompiles)
  check_cxx_source_compiles("
    #include <stdlib.h>
    #include <iconv.h>
    extern \"C\" size_t iconv (iconv_t cd, char * *inbuf, size_t *inbytesleft, char * *outbuf, size_t *outbytesleft);
    int main() { return 0; }\n"
    ICONV_NOT_REQUIRE_CONST
  )
  if(NOT ICONV_NOT_REQUIRE_CONST)
    set(ICONV_CONST const)
  endif()
endif()

configure_file(config.h.in "${CMAKE_CURRENT_SOURCE_DIR}/src/config.h")

add_library(
  mecab STATIC
  src/char_property.cpp
  src/char_property.h
  src/common.h
  src/config.h
  src/connector.cpp
  src/connector.h
  src/context_id.cpp
  src/context_id.h
  src/darts.h
  src/dictionary_compiler.cpp
  src/dictionary_generator.cpp
  src/dictionary_rewriter.cpp
  src/dictionary_rewriter.h
  src/dictionary.cpp
  src/dictionary.h
  src/eval.cpp
  src/feature_index.cpp
  src/feature_index.h
  src/freelist.h
  src/iconv_utils.cpp
  src/iconv_utils.h
  src/lbfgs.cpp
  src/lbfgs.h
  src/learner_node.h
  src/learner_tagger.cpp
  src/learner_tagger.h
  src/learner.cpp
  src/libmecab.cpp
  src/mecab.cpp
  src/mmap.h
  src/nbest_generator.cpp
  src/nbest_generator.h
  src/param.cpp
  src/param.h
  src/scoped_ptr.h
  src/stream_wrapper.h
  src/string_buffer.cpp
  src/string_buffer.h
  src/tagger.cpp
  src/thread.h
  src/tokenizer.cpp
  src/tokenizer.h
  src/ucs.h
  src/ucstable.h
  src/utils.cpp
  src/utils.h
  src/viterbi.cpp
  src/viterbi.h
  src/writer.cpp
  src/writer.h
  src/mecab.h)

if(Iconv_FOUND)
  target_link_libraries(mecab PRIVATE Iconv::Iconv)
endif()

if(CHARSET STREQUAL "utf8")
  target_compile_definitions(mecab PRIVATE MECAB_USE_UTF8_ONLY)
endif()

target_compile_definitions(mecab PRIVATE HAVE_CONFIG_H DIC_VERSION=102)
target_include_directories(mecab PUBLIC "$<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/src>")

add_executable(mecab-dict-index src/mecab-dict-index.cpp src/winmain.h)
target_link_libraries(mecab-dict-index PRIVATE mecab)
