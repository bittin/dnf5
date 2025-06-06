if(NOT (WITH_GO OR WITH_PERL5 OR WITH_PYTHON3 OR WITH_RUBY))
    return()
endif()


message("Building bindings")


find_package(SWIG REQUIRED)
include(UseSWIG)


if(CMAKE_SIZEOF_VOID_P EQUAL 8)
    # using 64bit architecture
    set(CMAKE_SWIG_FLAGS "-DSWIGWORDSIZE64")
endif()


include_directories(${CMAKE_SOURCE_DIR}/include)
include_directories(${CMAKE_SOURCE_DIR})


# SWIG is flooding logs with warnings we can't fix -> turn them off
set(SWIG_COMPILE_OPTIONS
    -Wno-cast-align
    -Wno-conversion
    -Wno-deprecated-copy
    -Wno-deprecated-declarations
    -Wno-ignored-qualifiers
    -Wno-missing-declarations
    -Wno-missing-field-initializers
    -Wno-sign-compare
    -Wno-sign-conversion
    -Wno-sometimes-uninitialized
    -Wno-strict-aliasing
    -Wno-unused-function
    -Wno-unused-parameter
)

if(CMAKE_CXX_COMPILER_ID MATCHES "Clang")
    list(APPEND SWIG_COMPILE_OPTIONS
        -Wno-deprecated-volatile
        -Wno-unknown-warning-option
    )
else()
    list(APPEND SWIG_COMPILE_OPTIONS
        -Wno-catch-value
        -Wno-maybe-uninitialized
        -Wno-volatile
    )
endif()


# list of all modules that will be included in libdnf5 bindings
list(APPEND SWIG_LIBDNF5_MODULES advisory base common comps conf exception logger repo rpm transaction plugin utils)


# list of all modules that will be included in libdnf5-cli bindings
list(APPEND SWIG_LIBDNF5_CLI_MODULES progressbar)


add_subdirectory(go)
add_subdirectory(perl5)
add_subdirectory(python3)
add_subdirectory(ruby)
