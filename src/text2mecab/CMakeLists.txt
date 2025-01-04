list(
  APPEND OPENJTALK_SOURCES
  "${CMAKE_CURRENT_LIST_DIR}/text2mecab_rule_ascii_for_euc_jp.h"
  "${CMAKE_CURRENT_LIST_DIR}/text2mecab_rule_ascii_for_shift_jis.h"
  "${CMAKE_CURRENT_LIST_DIR}/text2mecab_rule_ascii_for_utf_8.h"
  "${CMAKE_CURRENT_LIST_DIR}/text2mecab_rule_euc_jp.h"
  "${CMAKE_CURRENT_LIST_DIR}/text2mecab_rule_shift_jis.h"
  "${CMAKE_CURRENT_LIST_DIR}/text2mecab_rule_utf_8.h"
  "${CMAKE_CURRENT_LIST_DIR}/text2mecab.c"
  "${CMAKE_CURRENT_LIST_DIR}/text2mecab.h")
