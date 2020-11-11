cmake_minimum_required (VERSION 3.8)

project(log4cpp VERSION 1.0.0)

option(LOG4CPP_SUPPRESS_LOG_LOCATION "Do not print source location in logs" OFF)

set(log4cpp_public_headers
    ./include/log4cpp/HexLogging.h
    ./include/log4cpp/ILogHandler.h
    ./include/log4cpp/Location.h
    ./include/log4cpp/Logger.h
    ./include/log4cpp/LogLevels.h
    ./include/log4cpp/LogMacros.h
    ./include/log4cpp/MockLogHandler.h
    ./include/log4cpp/Strings.h
)

add_library(log4cpp INTERFACE)
#target_link_libraries(log4cpp INTERFACE ser4cpp)
target_include_directories(log4cpp INTERFACE 
    "$<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>"
    "$<INSTALL_INTERFACE:include>")
target_compile_features(log4cpp INTERFACE cxx_std_11)

if(${LOG4CPP_SUPPRESS_LOG_LOCATION})
    target_compile_definitions(log4cpp INTERFACE LOG4CPP_SUPPRESS_LOG_LOCATION)
endif()


include(CMakePackageConfigHelpers)
write_basic_package_version_file(
    "${PROJECT_BINARY_DIR}/log4cppConfigVersion.cmake"
    VERSION 1.0
    COMPATIBILITY AnyNewerVersion
)
install(TARGETS log4cpp
    EXPORT log4cppTargets
    LIBRARY DESTINATION lib COMPONENT Runtime
    ARCHIVE DESTINATION lib COMPONENT Development
    RUNTIME DESTINATION bin COMPONENT Runtime
    PUBLIC_HEADER DESTINATION include COMPONENT Development
    BUNDLE DESTINATION bin COMPONENT Runtime
    )
include(CMakePackageConfigHelpers)
configure_package_config_file(
    "${PROJECT_SOURCE_DIR}/cmake/log4cppConfig.cmake.in"
    "${PROJECT_BINARY_DIR}/log4cppConfig.cmake"
    INSTALL_DESTINATION lib/cmake/log4cpp
)
install(EXPORT log4cppTargets DESTINATION lib/cmake/log4cpp)
install(FILES "${PROJECT_BINARY_DIR}/log4cppConfigVersion.cmake"
              "${PROJECT_BINARY_DIR}/log4cppConfig.cmake"
        DESTINATION lib/cmake/log4cpp)
install(FILES ${log4cpp_public_headers} DESTINATION include/log4cpp)
