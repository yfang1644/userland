cmake_minimum_required(VERSION 2.8)

get_filename_component (VIDEOCORE_ROOT ../../../.. ABSOLUTE)
include (${VIDEOCORE_ROOT}/makefiles/cmake/global_settings.cmake)

if (NOT WIN32)
   add_definitions(-Wall)
endif ()

include_directories (
   ${VIDEOCORE_HEADERS_BUILD_DIR}
   ${VIDEOCORE_ROOT}
   ${VIDEOCORE_ROOT}/opensrc/helpers/libfdt
   ${VIDEOCORE_ROOT}/helpers/dtoverlay
)

add_executable(dtoverlay dtoverlay_main.c utils.c)
target_link_libraries(dtoverlay dtovl)
install(TARGETS dtoverlay RUNTIME DESTINATION bin)

add_custom_command(TARGET dtoverlay POST_BUILD COMMAND ln;-sf;dtoverlay;dtparam)
install(FILES ${CMAKE_CURRENT_BINARY_DIR}/dtparam DESTINATION bin)

set(DTOVERLAY_SCRIPTS dtoverlay-pre dtoverlay-post)
foreach(_script ${DTOVERLAY_SCRIPTS})
   add_custom_command(
     TARGET dtoverlay
     COMMAND ${CMAKE_COMMAND}
     -E copy ${CMAKE_CURRENT_SOURCE_DIR}/${_script}
     ${CMAKE_BINARY_DIR}/../../bin/${_script}
   )
endforeach()
install(PROGRAMS ${DTOVERLAY_SCRIPTS} DESTINATION bin)
