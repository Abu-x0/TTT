cmake_minimum_required(VERSION 3.16.0)
project(TicTacToe VERSION 1.0.0 LANGUAGES CXX)

set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

# Find required Qt components
find_package(Qt5 REQUIRED COMPONENTS Core Widgets Sql)

# Enable automatic MOC, UIC, and RCC processing
set(CMAKE_AUTOMOC ON)
set(CMAKE_AUTOUIC ON)
set(CMAKE_AUTORCC ON)

# Source files based on your combined.txt
set(SOURCES
    App.cpp
    choosedifficulty.cpp
    gamehistory.cpp
    gamemodewindow.cpp
    gameplaywindow.cpp
    loginpage.cpp
    main.cpp
    mainwindow.cpp
    playerselection.cpp
    signupform.cpp
    statisticswindow.cpp
    userprofile.cpp
)

# Header files based on your combined_h.txt
set(HEADERS
    App.h
    choosedifficulty.h
    gamehistory.h
    gamemodewindow.h
    gameplaywindow.h
    loginpage.h
    mainwindow.h
    playerselection.h
    signupform.h
    statisticswindow.h
    userprofile.h
)

# UI files (Qt Designer forms)
set(UI_FILES
    choosedifficulty.ui
    gamehistory.ui
    gamemodewindow.ui
    gameplaywindow.ui
    loginpage.ui
    mainwindow.ui
    playerselection.ui
    signupform.ui
    statisticswindow.ui
    userprofile.ui
)

# Create main executable
add_executable(TicTacToe ${SOURCES} ${HEADERS} ${UI_FILES})

# Link Qt libraries
target_link_libraries(TicTacToe
    Qt5::Core
    Qt5::Widgets
    Qt5::Sql
)

# Set UI files directory for Qt's UIC
set_target_properties(TicTacToe PROPERTIES
    AUTOUIC_SEARCH_PATHS "."
)

# Copy database file to build directory (if exists)
if(EXISTS "${CMAKE_CURRENT_SOURCE_DIR}/TicTacBoom.db")
    configure_file(
        "${CMAKE_CURRENT_SOURCE_DIR}/TicTacBoom.db"
        "${CMAKE_BINARY_DIR}/TicTacBoom.db"
        COPYONLY
    )
endif()

# Installation
install(TARGETS TicTacToe
    BUNDLE DESTINATION .
    RUNTIME DESTINATION bin
)

# Enable testing
enable_testing()

# Test configuration (optional)
option(BUILD_TESTS "Build test programs" OFF)

if(BUILD_TESTS)
    find_package(GTest REQUIRED)
    
    # Test executable
    add_executable(TicTacToeTests
        tests/test_main.cpp
        tests/test_game_logic.cpp
        App.cpp  # Include only core logic for testing
    )
    
    target_link_libraries(TicTacToeTests
        GTest::gtest_main
        Qt5::Core
        Qt5::Test
    )
    
    # Add test
    add_test(NAME TicTacToeTests COMMAND TicTacToeTests)
endif()

# Coverage flags for debug builds
if(CMAKE_BUILD_TYPE STREQUAL "Debug")
    if(CMAKE_COMPILER_IS_GNUCXX)
        target_compile_options(TicTacToe PRIVATE --coverage)
        target_link_options(TicTacToe PRIVATE --coverage)
    endif()
endif()

# Compiler-specific options
if(MSVC)
    target_compile_options(TicTacToe PRIVATE /W4)
else()
    target_compile_options(TicTacToe PRIVATE -Wall -Wextra -Wpedantic)
endif()

# Set output directories
set_target_properties(TicTacToe PROPERTIES
    RUNTIME_OUTPUT_DIRECTORY "${CMAKE_BINARY_DIR}/bin"
    LIBRARY_OUTPUT_DIRECTORY "${CMAKE_BINARY_DIR}/lib"
    ARCHIVE_OUTPUT_DIRECTORY "${CMAKE_BINARY_DIR}/lib"
)
