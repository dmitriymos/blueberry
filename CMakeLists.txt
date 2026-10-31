cmake_minimum_required(VERSION 3.15)
project(blueberry VERSION 1.0.0 LANGUAGES CXX)

set(CMAKE_CXX_STANDARD 23)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
set(CMAKE_EXPORT_COMPILE_COMMANDS ON)

include(cmake/CPM.cmake)
set(TARGET ${PROJECT_NAME})
set(TEST_TARGET ${PROJECT_NAME}_test)

# Библиотека
add_library(${TARGET} SHARED 
    src/containers/array.cpp
)

target_include_directories(${TARGET} PUBLIC ${CMAKE_CURRENT_SOURCE_DIR}/src)

# Указываем, что это shared библиотека с экспортом
target_compile_definitions(${PROJECT_NAME} PRIVATE UNTITLED_EXPORTS)

# Тесты
if(BLUEBERRY_BUILD_TEST)
    enable_testing()
    
    CPMAddPackage(
        NAME googletest
        GITHUB_REPOSITORY google/googletest
        VERSION 1.14.0
        OPTIONS
            "INSTALL_GTEST OFF"
            "INSTALL_GMOCK OFF"
            "gtest_force_shared_crt ON"
    )
    
    add_executable(${TEST_TARGET}
        test/
    )

    target_include_directories(${TEST_TARGET}
        PRIVATE
            ${CMAKE_CURRENT_SOURCE_DIR}/src
            ${googletest_SOURCE_DIR}/googletest/include
            ${googletest_SOURCE_DIR}/googlemock/include
    )
    target_link_libraries(${TEST_TARGET} PRIVATE ${TARGET} GTest::gtest_main)
    
    include(CTest)
    add_test(NAME ${TEST_TARGET} 
        COMMAND ${TEST_TARGET}
    )
    include(GoogleTest)
    gtest_discover_tests(${TEST_TARGET})
endif()