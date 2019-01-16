cmake_minimum_required(VERSION 3.5)
project(Lua VERSION 5.3.5 LANGUAGES C CXX)

if(CMAKE_INSTALL_PREFIX_INITIALIZED_TO_DEFAULT)
  set(CMAKE_INSTALL_PREFIX ${CMAKE_SOURCE_DIR}/dist CACHE PATH "..." FORCE)
endif()

add_library(lua
	src/lapi.c src/lcode.c src/lctype.c src/ldebug.c src/ldo.c src/ldump.c src/lfunc.c src/lgc.c src/llex.c src/lmem.c src/lobject.c src/lopcodes.c src/lparser.c src/lstate.c src/lstring.c src/ltable.c src/ltm.c src/lundump.c src/lvm.c src/lzio.c 
	src/lauxlib.c src/lbaselib.c src/lbitlib.c src/lcorolib.c src/ldblib.c src/liolib.c src/lmathlib.c src/loslib.c src/lstrlib.c src/ltablib.c src/lutf8lib.c src/loadlib.c src/linit.c
)
target_compile_definitions(lua PUBLIC -DLUA_COMPAT_MODULE)
target_include_directories(lua
    PUBLIC
        $<INSTALL_INTERFACE:include>
        $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>
    PRIVATE
        ${CMAKE_CURRENT_SOURCE_DIR}/src
)

set(PROJ_CONFIG_DIR lib/cmake/lua)
set(PROJ_EXPORT_NAME Lua)
set(PROJ_EXPORT_NAMESPACE Lua::)
set(PROJ_TARGET lua)
add_library("${PROJ_EXPORT_NAMESPACE}${PROJ_EXPORT_NAME}" ALIAS lua)
set_target_properties(${PROJ_TARGET} PROPERTIES EXPORT_NAME ${PROJ_EXPORT_NAME})

install(TARGETS ${PROJ_TARGET}
    EXPORT  ${PROJ_EXPORT_NAME}Targets
    LIBRARY DESTINATION lib
    ARCHIVE DESTINATION lib
    RUNTIME DESTINATION bin
    INCLUDES DESTINATION include
)

install(DIRECTORY include/ DESTINATION include)
install(EXPORT ${PROJ_EXPORT_NAME}Targets
    FILE
        ${PROJ_EXPORT_NAME}Targets.cmake 
    NAMESPACE
        ${PROJ_EXPORT_NAMESPACE}
    DESTINATION
        ${PROJ_CONFIG_DIR}
)

include(CMakePackageConfigHelpers)
write_basic_package_version_file(
    ${CMAKE_CURRENT_BINARY_DIR}/${PROJ_EXPORT_NAME}ConfigVersion.cmake
    VERSION ${PROJECT_VERSION}
    COMPATIBILITY AnyNewerVersion
)

configure_package_config_file(${PROJ_EXPORT_NAME}Config.cmake
    ${CMAKE_CURRENT_BINARY_DIR}/${PROJ_EXPORT_NAME}Config.cmake
    INSTALL_DESTINATION ${PROJ_CONFIG_DIR}
)
install(FILES
    ${CMAKE_CURRENT_BINARY_DIR}/${PROJ_EXPORT_NAME}Config.cmake
    ${CMAKE_CURRENT_BINARY_DIR}/${PROJ_EXPORT_NAME}ConfigVersion.cmake
    DESTINATION ${PROJ_CONFIG_DIR}
)

export(EXPORT ${PROJ_EXPORT_NAME}Targets FILE ${CMAKE_CURRENT_BINARY_DIR}/${PROJ_EXPORT_NAME}Targets.cmake  NAMESPACE ${PROJ_EXPORT_NAMESPACE})
export(PACKAGE ${PROJ_EXPORT_NAME})


add_executable(luax src/lua.c)
target_link_libraries(luax PRIVATE lua)

add_executable(luac src/luac.c)
target_link_libraries(luac PRIVATE lua)

#add_subdirectory(test)