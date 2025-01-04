list(
  APPEND OPENJTALK_SOURCES
  "${CMAKE_CURRENT_LIST_DIR}/jpcommon_label.c"
  "${CMAKE_CURRENT_LIST_DIR}/jpcommon_node.c"
  "${CMAKE_CURRENT_LIST_DIR}/jpcommon_rule_ascii_for_euc_jp.h"
  "${CMAKE_CURRENT_LIST_DIR}/jpcommon_rule_ascii_for_shift_jis.h"
  "${CMAKE_CURRENT_LIST_DIR}/jpcommon_rule_ascii_for_utf_8.h"
  "${CMAKE_CURRENT_LIST_DIR}/jpcommon_rule_euc_jp.h"
  "${CMAKE_CURRENT_LIST_DIR}/jpcommon_rule_shift_jis.h"
  "${CMAKE_CURRENT_LIST_DIR}/jpcommon_rule_utf_8.h"
  "${CMAKE_CURRENT_LIST_DIR}/jpcommon.c"
  "${CMAKE_CURRENT_LIST_DIR}/jpcommon.h")
