cmake_minimum_required (VERSION 2.8.7)
project (OpenMAMA-omnm)

# Note path is relative to where it would be executed
set(DEFAULT_MAMA_SRC ../../OpenMAMA)

# Transact & BusTalk are not compatible w/C++11 yet
set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -std=gnu++98 -D_GLIBCXX_USE_CXX11_ABI=0")
set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -std=gnu99")

# enable warnings
set(WARNFLAGS "${WARNFLAGS} -Wall")
set(WARNFLAGS "${WARNFLAGS} -Wextra")
set(WARNFLAGS "${WARNFLAGS} -Wformat")
set(WARNFLAGS "${WARNFLAGS} -Wformat-nonliteral")                # warn about non-literal format strings in printf etc.
#set(WARNFLAGS "${WARNFLAGS} -Wexit-time-destructors")
# disable warnings
set(WARNFLAGS "${WARNFLAGS} -Wno-reorder")                       # order of initialization in ctor
set(WARNFLAGS "${WARNFLAGS} -Wno-unused-parameter")              # given that API is defined in interface, this is kind of hard to enforce
set(WARNFLAGS "${WARNFLAGS} -Wno-ignored-qualifiers")            # e.g., const on value return types

# use address sanitizer? (same params for gcc/clang)
if("$ENV{BUILD_TYPE}" STREQUAL "asan")
  set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -fsanitize=address -fsanitize-address-use-after-scope -fno-common")
  set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -fsanitize=address -fsanitize-address-use-after-scope")
  set(CMAKE_SHARED_LINKER_FLAGS "${CMAKE_SHARED_LINKER_FLAGS} -fsanitize=address -fsanitize-address-use-after-scope")
  set(CMAKE_EXE_LINKER_FLAGS "${CMAKE_EXE_LINKER_FLAGS} -fsanitize=address -fsanitize-address-use-after-scope")
endif()

# use thread sanitizer? (same params for gcc/clang)
if("$ENV{BUILD_TYPE}" STREQUAL "tsan")
  set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -fsanitize=thread -fPIE")
  set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -fsanitize=thread -fPIE")
  set(CMAKE_SHARED_LINKER_FLAGS "${CMAKE_SHARED_LINKER_FLAGS} -fsanitize=thread  ")
  set(CMAKE_EXE_LINKER_FLAGS "${CMAKE_EXE_LINKER_FLAGS} -fsanitize=thread -pie ")
endif()

set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} ${WARNFLAGS}")

if(UNIX)
    file(GLOB DEFAULT_MAMA_ROOT ../OpenMAMA/openmama_install_*)
    set(DEFAULT_GTEST_ROOT "/usr/local")
else()
    if(CMAKE_CL_64)
        set(DEFAULT_INSTALL_PREFIX $ENV{ProgramW6432})
    else()
        set(DEFAULT_INSTALL_PREFIX $ENV{PROGRAMFILES})
    endif()

    # Note path is relative to where it would be used
    set(DEFAULT_MAMA_SRC ../../OpenMAMA)
    set(DEFAULT_MAMA_ROOT "${DEFAULT_INSTALL_PREFIX}/OpenMAMA")
endif()

if(NOT MAMA_SRC)
    set(MAMA_SRC ${DEFAULT_MAMA_SRC})
endif()

if(NOT MAMA_ROOT)
    set(MAMA_ROOT ${DEFAULT_MAMA_ROOT})
endif()


if((NOT "$ENV{BUILD_TYPE}" STREQUAL "tsan") AND (NOT GTEST_ROOT))
    set(GTEST_ROOT ${DEFAULT_GTEST_ROOT})
endif()

add_subdirectory(src)
