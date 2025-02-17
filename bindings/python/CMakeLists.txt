cmake_minimum_required(VERSION 3.10)

if (NOT BUILD_SHARED_LIBS)
  message(FATAL_ERROR "Cannot build Python bindings with a static lib build: "
    "set BUILD_SHARED_LIBS to ON.")
endif()

include(${CMAKE_MODULE_PATH}/Buildpybind11.cmake)
include(${CMAKE_MODULE_PATH}/pybind11Tools.cmake)

function (add_pybind11_extension ext_name)
  string(REPLACE "_" ";" modlist ${ext_name})
  list(GET modlist -1 modname)
  list(REMOVE_AT modlist -1)
  if(modlist)
    string(REPLACE ";" "/" relpath "${modlist}")
  else()
    set(relpath "")
  endif()

  pybind11_add_module(
    ${ext_name}
    ${CMAKE_CURRENT_LIST_DIR}/${relpath}/_${modname}.cpp
    )

  target_link_libraries(
    ${ext_name}
    PUBLIC
    flashlight-text
    )

  target_include_directories(
    ${ext_name}
    PRIVATE
    ${PROJECT_SOURCE_DIR}
    )

  add_dependencies(${ext_name} pybind11)
  if (CMAKE_LIBRARY_OUTPUT_DIRECTORY)
    set_target_properties(${ext_name} PROPERTIES LIBRARY_OUTPUT_DIRECTORY ${CMAKE_LIBRARY_OUTPUT_DIRECTORY}/${relpath})
  endif()
  set_target_properties(${ext_name} PROPERTIES OUTPUT_NAME ${ext_name})
endfunction ()

add_pybind11_extension(flashlight_lib_text_decoder)
add_pybind11_extension(flashlight_lib_text_dictionary)
