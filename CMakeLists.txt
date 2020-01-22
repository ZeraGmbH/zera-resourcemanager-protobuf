cmake_minimum_required(VERSION 3.1)

#set project name and language
project(zera-resourcemanager-protobuf LANGUAGES CXX C)

#Automoc and RCC ON. Needed in QT projects
#set(CMAKE_AUTOMOC ON)
#set(CMAKE_AUTORCC ON)
#C++ 11 Flag
set(CMAKE_CXX_STANDARD 11)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

#Set project Version
set(zeraprotobuf_VERSION_MAJOR "0")
set(zeraprotobuf_VERSION_MINOR "0")
set(zeraprotobuf_VERSION_PATCH "1")

set(PROJECT_VERSION "${zeraprotobuf_VERSION_MAJOR}.${zeraprotobuf_VERSION_MINOR}.${zeraprotobuf_VERSION_PATCH}")

#Adding cmake functions like feature_summary()
include(FeatureSummary)
include(GNUInstallDirs)

#Find dependecies
find_package(Protobuf REQUIRED)


PROTOBUF_GENERATE_CPP(PROTO_SRCS PROTO_HDRS ${PROJECT_SOURCE_DIR}/resourcemanager/netmessages.proto)


#Find dependecies
file(GLOB SOURCES 
    ${PROJECT_SOURCE_DIR}/resourcemanager/*.cpp
    ${CMAKE_CURRENT_BINARY_DIR}/*.cpp
    ${PROJECT_SOURCE_DIR}/resourcemanager/*.cc
    ${CMAKE_CURRENT_BINARY_DIR}/*.cc
    )

set(SOURCES ${SOURCES}  ${PROTO_SRCS})

file(GLOB HEADER
    ${PROJECT_SOURCE_DIR}/resourcemanager/*.h
    ${CMAKE_CURRENT_BINARY_DIR}/*.h
    )

set(HEADER ${HEADER}  ${PROTO_HDRS})

file(GLOB RESOURCES 
    ${PROJECT_SOURCE_DIR}/resourcemanager/*.qrc
    )

#create library 
add_library(zeraprotobuf SHARED
    ${SOURCES}
    ${HEADER}
    ${RESOURCES}
    )


#link dependencies
target_link_libraries(zeraprotobuf
    PRIVATE
    ${Protobuf_LIBRARIES}
    )

# announce headers - target perspective
target_include_directories(zeraprotobuf
    PUBLIC
    $<INSTALL_INTERFACE:include/zeraprotobuf>
    $<BUILD_INTERFACE:${CMAKE_CURRENT_BINARY_DIR}>
    PRIVATE
    ${CMAKE_CURRENT_SOURCE_DIR}
    )

#set target Version
set_target_properties(zeraprotobuf PROPERTIES OUTPUT_NAME "zera-protobuf")
set_target_properties(zeraprotobuf PROPERTIES VERSION ${PROJECT_VERSION})
set_target_properties(zeraprotobuf PROPERTIES SOVERSION ${zeraprotobuf_VERSION_MAJOR})


#generate export header
include(GenerateExportHeader)
generate_export_header(zeraprotobuf)

#install export header
install(FILES
    ${CMAKE_CURRENT_BINARY_DIR}/zeraprotobuf_export.h
    DESTINATION ${CMAKE_INSTALL_INCLUDEDIR}/zeraprotobuf
    )



# install library
install(TARGETS zeraprotobuf
    EXPORT zeraprotobufExport
    LIBRARY DESTINATION ${CMAKE_INSTALL_LIBDIR}
    )

# configure MarkdownQtConfigVersion.cmake
include(CMakePackageConfigHelpers)
write_basic_package_version_file(
    zeraprotobufConfigVersion.cmake
    VERSION ${PACKAGE_VERSION}
    COMPATIBILITY SameMinorVersion
    )

# configure MarkdownQtConfig.cmake
configure_file(zeraprotobufConfig.cmake.in zeraprotobufConfig.cmake @ONLY)


# install MarkdownQtConfig(Version).cmake
install(FILES "${CMAKE_CURRENT_BINARY_DIR}/zeraprotobufConfig.cmake"
    "${CMAKE_CURRENT_BINARY_DIR}/zeraprotobufConfigVersion.cmake"
    DESTINATION ${CMAKE_INSTALL_LIBDIR}/cmake/zeraprotobuf
    )

# install targets cmake-files
install(EXPORT zeraprotobufExport
    FILE zeraprotobufTargets.cmake
    NAMESPACE VeinMeta::
    DESTINATION ${CMAKE_INSTALL_LIBDIR}/cmake/zeraprotobuf
    )

# install public headers
install(
    FILES ${HEADER}
    DESTINATION ${CMAKE_INSTALL_INCLUDEDIR}/zeraprotobuf
    )

# spawn out some info on configuration
feature_summary(WHAT ALL FATAL_ON_MISSING_REQUIRED_PACKAGES)




