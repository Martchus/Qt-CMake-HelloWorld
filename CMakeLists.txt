cmake_minimum_required(VERSION 2.8.11)

project(helloworld)

# Find includes in the build directories
set(CMAKE_INCLUDE_CURRENT_DIR ON)

option(STATIC_QT "Use static Qt5 libraries" ON)

if(STATIC_QT)
    set(QT_PREFIX StaticQt5)
    set(LINKER_FLAGS -static -static-libstdc++ -static-libgcc)
    find_package(Qt5Core REQUIRED)
else()
    set(QT_PREFIX Qt5)
endif()

set(QT5_PKGS Core Widgets Gui Network Sql)

set(QT5_LIBS "")

foreach(pkg ${QT5_PKGS})
    find_package(${QT_PREFIX}${pkg} REQUIRED)
    list(APPEND QT5_LIBS ${QT_PREFIX}::${pkg})
endforeach()

list(APPEND QT5_LIBS ${QT_PREFIX}::QWindowsIntegrationPlugin)
list(APPEND QT5_LIBS ${QT_PREFIX}::QWindowsVistaStylePlugin)

# Turn on automatic invocation of the MOC
set(CMAKE_AUTOMOC ON)
set(CMAKE_AUTOUIC ON)
set(CMAKE_AUTORCC ON)

# There may be a way to tell up front if Qt5 is going to be found, but I haven't found
# a foolproof way to do it yet, so settle for the default error message for now.
#if(NOT CMAKE_PREFIX_PATH AND NOT Qt5Widgets_DIR)
#    message(WARNING "CMAKE_PREFIX_PATH is not defined, so find_package may not work. Set the CMAKE_PREFIX_PATH "
#            "environment variable to the install prefix of Qt 5, either on the command line as "
#            "-DCMAKE_PREFIX_PATH=\"path/to/Qt5/lib/cmake\" or with set(CMAKE_PREFIX_PATH path/to/Qt5/lib/cmake)")
#endif(NOT CMAKE_PREFIX_PATH AND NOT Qt5Widgets_DIR)

# Tell CMake to create the helloworld executable
add_executable(helloworld main.cpp mainwindow.cpp mainwindow.ui resources.qrc)

# Add the Qt5 Widgets for linking
target_link_libraries(helloworld ${LINKER_FLAGS} ${QT5_LIBS})

if(STATIC_QT)
set_target_properties(helloworld PROPERTIES
    LINK_SEARCH_START_STATIC ON
    LINK_SEARCH_END_STATIC ON
)
endif()
