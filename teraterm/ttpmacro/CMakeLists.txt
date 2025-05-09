﻿set(PACKAGE_NAME "ttpmacro")

project(${PACKAGE_NAME})

include(${CMAKE_CURRENT_SOURCE_DIR}/../../libs/lib_oniguruma.cmake)
include(${CMAKE_CURRENT_SOURCE_DIR}/../../libs/lib_SFMT.cmake)
include(${CMAKE_CURRENT_SOURCE_DIR}/../../libs/lib_libressl.cmake)

add_executable(
  ${PACKAGE_NAME} WIN32
  ttm_res.h
  errdlg.cpp
  errdlg.h
  inpdlg.cpp
  inpdlg.h
  ListDlg.cpp
  ListDlg.h
  msgdlg.cpp
  msgdlg.h
  statdlg.cpp
  statdlg.h
  macrodlgbase.h
  ttl.cpp
  ttl.h
  ttl_gui.cpp
  ttl_gui.h
  ttm_res.h
  ttmacro.cpp
  ttmacro.h
  ttmbuff.c
  ttmbuff.h
  ttmdde.c
  ttmdde.h
  ttmdef.h
  ttmdlg.cpp
  ttmdlg.h
  ttmenc.c
  ttmenc2.c
  ttmenc.h
  ttmenc2.h
  ttmlib.c
  ttmlib.h
  ttmmain.cpp
  ttmmain.h
  ttmmsg.h
  ttmonig.h
  ttmparse.cpp
  ttmparse.h
  wait4all.c
  wait4all.h

  ttpmacro.rc
  ttm-version.rc
  ttmacro_manifest.rc
  )

if(SUPPORT_OLD_WINDOWS)
  if(MSVC)
    target_sources(
      ${PACKAGE_NAME}
      PRIVATE
      ../../teraterm/common/compat_w95_vs2005.c
      )
  endif()
  if(MINGW)
    target_link_libraries(
      ${PACKAGE_NAME}
      PRIVATE
      -Wl,--whole-archive
      mingw_msvcrt
      -Wl,--no-whole-archive
      )
  endif()
endif(SUPPORT_OLD_WINDOWS)

target_sources(
  ${PACKAGE_NAME}
  PRIVATE
  ../common/teraterm.h
  ../common/ttcommon.h
  ../common/ttddecmnd.h
  ../common/tttypes.h
  ../common/tt-version.h
  ../common/i18n.h
  ../common/ttlib.c
  ../common/ttlib.h
  ../common/tmfc.h
  ../common/compat_win.h
  ../common/codeconv.h
  ../common/dllutil.h
  )

source_group(
  "common"
  REGULAR_EXPRESSION
  "..\/common\/"
  )

set_target_properties(
  ${PACKAGE_NAME}
  PROPERTIES
  RUNTIME_OUTPUT_DIRECTORY "${CMAKE_BINARY_DIR}/"
  )

target_include_directories(
  ${PACKAGE_NAME}
  PRIVATE
  ${ONIGURUMA_INCLUDE_DIRS}
  ${SFMT_INCLUDE_DIRS}
  ${LIBRESSL_INCLUDE_DIRS}
  ${CMAKE_CURRENT_SOURCE_DIR}/../ttpfile
  )

if (MSVC)
  set(CMAKE_EXE_LINKER_FLAGS "${CMAKE_EXE_LINKER_FLAGS} /MANIFEST:NO")
endif()

target_link_libraries(
  ${PACKAGE_NAME}
  PRIVATE
  common_static
  ttpcmn
  ${ONIGURUMA_LIB}
  ${SFMT_LIB}
  ${LIBRESSL_LIB}
  )

if(SUPPORT_OLD_WINDOWS)
  target_link_libraries(
    ${PACKAGE_NAME}
    PRIVATE
    layer_for_unicode
    )
endif()

target_link_libraries(
  ${PACKAGE_NAME}
  PRIVATE
  ws2_32
  comctl32
  gdi32
  comdlg32
  bcrypt
)

if(MSVC)
  if(${CMAKE_VERSION} VERSION_GREATER "3.13")
    target_link_options(
      ${PACKAGE_NAME}
      PRIVATE
      /DEBUG:FULL
      /PDBALTPATH:${PACKAGE_NAME}.pdb
      /map
    )
  endif()
endif()

install(
  TARGETS ${PACKAGE_NAME}
  DESTINATION .
)

if(MSVC)
  install(
    FILES
    $<TARGET_PDB_FILE:${PACKAGE_NAME}>
    $<CONFIG>/ttpmacro.map
    DESTINATION .
  )
endif()
