
set(TARGET Wavefront)

set(HEADER_LIST Wavefront.hpp WavefrontParser.hpp)
list(TRANSFORM HEADER_LIST PREPEND "${${PROJECT_NAME}_SOURCE_DIR}/include/")

set(SOURCE_LIST Wavefront.cpp WavefrontParser.cpp)
list(TRANSFORM SOURCE_LIST PREPEND "${${PROJECT_NAME}_SOURCE_DIR}/src/")

add_library(${TARGET} ${SOURCE_LIST} ${HEADER_LIST})

target_include_directories(${TARGET} PUBLIC
    $<BUILD_INTERFACE:${${PROJECT_NAME}_SOURCE_DIR}/include>
    $<INSTALL_INTERFACE:${CMAKE_INSTALL_INCLUDEDIR}>)

target_compile_features(${TARGET} PUBLIC cxx_std_17)

target_link_libraries(${TARGET} PUBLIC glm)

set_property(TARGET ${TARGET} PROPERTY POSITION_INDEPENDENT_CODE ON)

# Check for Inter Procedural Optimization (IPO)
include(CheckIPOSupported)
check_ipo_supported(RESULT result)
if(result)
    set_target_properties(${TARGET} PROPERTIES INTERPROCEDURAL_OPTIMIZATION TRUE)
endif()

# Add ALIAS target for use in add_subdirectory or build directory
add_library(${PROJECT_NAME}::${TARGET} ALIAS ${TARGET})

# IDE support, show headers in a folder
source_group(TREE "${PROJECT_SOURCE_DIR}/include"
    PREFIX "Header Files"
    FILES ${HEADER_LIST})

# Add target to Project Targets for install
install(TARGETS ${TARGET}
    EXPORT ${PROJECT_NAME}Targets
    ARCHIVE DESTINATION ${CMAKE_INSTALL_LIBDIR}
    LIBRARY DESTINATION ${CMAKE_INSTALL_LIBDIR}
    RUNTIME DESTINATION ${CMAKE_INSTALL_BINDIR}) # This is for Windows?

# Include the headers in install
install(DIRECTORY "${${PROJECT_NAME}_SOURCE_DIR}/include/"
    DESTINATION ${CMAKE_INSTALL_INCLUDEDIR})
