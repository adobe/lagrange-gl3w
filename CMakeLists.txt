#
# Copyright 2020 Adobe. All rights reserved.
# This file is licensed to you under the Apache License, Version 2.0 (the "License");
# you may not use this file except in compliance with the License. You may obtain a copy
# of the License at http://www.apache.org/licenses/LICENSE-2.0
#
# Unless required by applicable law or agreed to in writing, software distributed under
# the License is distributed on an "AS IS" BASIS, WITHOUT WARRANTIES OR REPRESENTATIONS
# OF ANY KIND, either express or implied. See the License for the specific language
# governing permissions and limitations under the License.
#

cmake_minimum_required(VERSION 3.11 FATAL_ERROR)

project(gl3w)

if(NOT GL3W_GENERATE)

    # use pre-generated files
    add_library(gl3w
        "${PROJECT_SOURCE_DIR}/src/gl3w.c"
        "${PROJECT_SOURCE_DIR}/include/GL/gl3w.h"
        "${PROJECT_SOURCE_DIR}/include/GL/glcorearb.h")

    target_include_directories(gl3w SYSTEM PUBLIC "${PROJECT_SOURCE_DIR}/include")

else()
    include(FetchContent)

    # generate files. Requires python.
    FetchContent_Declare(
        gl3w
        GIT_REPOSITORY https://github.com/skaslev/gl3w.git
        GIT_TAG 7729692af8a2322cddb636b90393a42c130b1c85)
    FetchContent_GetProperties(gl3w)
    if(gl3w_POPULATED)
        return()
    endif()
    FetchContent_Populate(gl3w)

    # gl3w builds its source files from a Python script, so we need to find the
    # Python interpreter and call it on the script before we can do anything else.
    find_package(PythonInterp REQUIRED)
    if(NOT ${PYTHONINTERP_FOUND})
        message(FATAL_ERROR "could not find Python interpreter -- needed to build gl3w")
    endif()

    find_file(gl3w_GEN_PY "gl3w_gen.py" PATHS "${gl3w_SOURCE_DIR}" NO_DEFAULT_PATH)
    if(NOT gl3w_GEN_PY)
        message(FATAL_ERROR "could not find gl3w_gen.py")
    endif()

    execute_process(
        COMMAND ${PYTHON_EXECUTABLE} ${gl3w_GEN_PY}
        WORKING_DIRECTORY ${gl3w_SOURCE_DIR}
        RESULT_VARIABLE gl3w_GEN_PY_RESULT)
    if(NOT gl3w_GEN_PY_RESULT EQUAL "0")
        message(FATAL_ERROR "bad exit status from call to gl3w_gen.py")
    endif()


    # Now that the gl3w sources have been created, we can add the library.
    add_library(gl3w)
    target_sources(gl3w PRIVATE
        "${gl3w_SOURCE_DIR}/src/gl3w.c")

    target_include_directories(gl3w PUBLIC "${gl3w_SOURCE_DIR}/include")

endif()

set_target_properties(gl3w PROPERTIES FOLDER third_party)
