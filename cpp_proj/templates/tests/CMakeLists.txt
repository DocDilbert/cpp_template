if (CMAKE_PARTS MATCHES "Visual Studio")
    # For Windows: Prevent overriding the parent project's compiler/linker settings
    set(gtest_force_shared_crt ON CACHE BOOL "" FORCE)
endif()

add_subdirectory("${PROJECT_SOURCE_DIR}/extern/googletest" "extern/googletest")

mark_as_advanced(
    BUILD_GMOCK BUILD_GTEST BUILD_SHARED_LIBS
    gmock_build_tests gtest_build_samples gtest_build_tests
    gtest_disable_pthreads gtest_force_shared_crt gtest_hide_internal_symbols
)

macro(package_add_test TESTNAME)
    # create an executable in which the tests will be stored
    add_executable(${TESTNAME} ${ARGN})
    # link the Google test infrastructure, mocking library, and a default main function to
    # the test executable.  Remove g_test_main if writing your own main function.
    target_link_libraries(${TESTNAME} PUBLIC gtest gmock)
    target_link_libraries(${TESTNAME} PUBLIC gtest_main)
{% if lib is defined%}
    target_link_libraries(${TESTNAME} PUBLIC {{lib.specs.dir}})
{% endif %}

    # gtest_discover_tests replaces gtest_add_tests,
    # see https://cmake.org/cmake/help/v3.10/module/GoogleTest.html for more options to pass to it
    gtest_discover_tests(${TESTNAME}
        # set a working directory so your project root so that you can find test data via paths relative to the project root
        WORKING_DIRECTORY ${PROJECT_DIR}
        PROPERTIES VS_DEBUGGER_WORKING_DIRECTORY "${PROJECT_DIR}"
    )
    set_target_properties(${TESTNAME} PROPERTIES FOLDER tests)
endmacro()

set(TEST_SOURCES
{% if 'basic' in test%}
    test_basic.cpp
{% endif %}
{% if 'lib' in test%}
    test_{{lib.specs.dir}}.cpp
{% endif %}
{% if 'clean_architecture_tests' in test%}
    {{lib.specs.dir}}/use_cases/test_sample_interactor.cpp
{% endif %}
)

package_add_test(ptest ${TEST_SOURCES})
