set(COMPILED_DICTIONARY_DIR "${CMAKE_CURRENT_BINARY_DIR}/dic")

if(CHARSET STREQUAL "sjis")
  set(OUTPUT_DICTIONARY_CHARSET CP932)
elseif(CHARSET STREQUAL "eucjp")
  set(OUTPUT_DICTIONARY_CHARSET EUC-JP)
elseif(CHARSET STREQUAL "utf8")
  set(OUTPUT_DICTIONARY_CHARSET UTF-8)
else()
  message(FATAL_ERROR "Encoding ${CHARSET} not recognized. You can set sjis/eucjp/utf8")
endif()

add_custom_command(
  OUTPUT
  dic/char.bin
  dic/COPYING
  dic/left-id.def
  dic/matrix.bin
  dic/pos-id.def
  dic/rewrite.def
  dic/right-id.def
  dic/sys.dic
  dic/unk.dic
  COMMAND ${CMAKE_COMMAND} -E copy_if_different _left-id.def left-id.def
  COMMAND ${CMAKE_COMMAND} -E copy_if_different _rewrite.def rewrite.def
  COMMAND ${CMAKE_COMMAND} -E copy_if_different _pos-id.def pos-id.def
  COMMAND ${CMAKE_COMMAND} -E copy_if_different _right-id.def right-id.def
  COMMAND ${CMAKE_COMMAND} -E make_directory "${COMPILED_DICTIONARY_DIR}"
  COMMAND mecab-dict-index -d . -o "${COMPILED_DICTIONARY_DIR}" -f UTF-8 -t ${OUTPUT_DICTIONARY_CHARSET}
  COMMAND ${CMAKE_COMMAND} -E copy_if_different left-id.def "${COMPILED_DICTIONARY_DIR}"
  COMMAND ${CMAKE_COMMAND} -E copy_if_different rewrite.def "${COMPILED_DICTIONARY_DIR}"
  COMMAND ${CMAKE_COMMAND} -E copy_if_different pos-id.def "${COMPILED_DICTIONARY_DIR}"
  COMMAND ${CMAKE_COMMAND} -E copy_if_different right-id.def "${COMPILED_DICTIONARY_DIR}"
  COMMAND ${CMAKE_COMMAND} -E copy_if_different COPYING "${COMPILED_DICTIONARY_DIR}"
  DEPENDS _left-id.def _rewrite.def _pos-id.def _right-id.def COPYING feature.def matrix.def naist-jdic.csv unidic-csj.csv unk.def
  WORKING_DIRECTORY "${CMAKE_CURRENT_SOURCE_DIR}"
)
add_custom_target(
  mecab_naist_jdic ALL
  DEPENDS
  dic/char.bin
  dic/COPYING
  dic/left-id.def
  dic/matrix.bin
  dic/pos-id.def
  dic/rewrite.def
  dic/right-id.def
  dic/sys.dic
  dic/unk.dic
)
install(DIRECTORY "${COMPILED_DICTIONARY_DIR}" DESTINATION bin)
