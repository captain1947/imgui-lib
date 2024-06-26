cmake_minimum_required(VERSION 3.10)

project(imgui)

set(CMAKE_EXPORT_COMPILE_COMMANDS ON)

if (NOT ANDROID)
	set(CMAKE_INSTALL_PREFIX $ENV{PREFIX})
	include(GNUInstallDirs)
endif()

set(SRC_PATH src)
file(GLOB HEADERS ${SRC_PATH}/*.h)
file(GLOB SOURCES ${SRC_PATH}/*.cpp)

set(LIBS SDL3::SDL3)

if (NOT ANDROID)
	find_package(SDL3 REQUIRED)
endif()

add_library(${PROJECT_NAME} SHARED ${HEADERS} ${SOURCES})

target_compile_options(${PROJECT_NAME} PUBLIC -Wall -Wextra -Wpedantic)

target_link_libraries(${PROJECT_NAME} PUBLIC ${LIBS})

# Setting which header files should be supplied with the library
set_target_properties(${PROJECT_NAME} PROPERTIES PUBLIC_HEADER "${HEADERS}")

if (NOT ANDROID)
	install(TARGETS ${PROJECT_NAME}
			EXPORT "${PROJECT_NAME}-config"
			RUNTIME DESTINATION "${CMAKE_INSTALL_BINDIR}"
			LIBRARY DESTINATION "${CMAKE_INSTALL_LIBDIR}"
			ARCHIVE DESTINATION "${CMAKE_INSTALL_LIBDIR}"
			PUBLIC_HEADER DESTINATION "${CMAKE_INSTALL_INCLUDEDIR}/${PROJECT_NAME}"
			PRIVATE_HEADER DESTINATION "${CMAKE_INSTALL_INCLUDEDIR}/${PROJECT_NAME}"
			INCLUDES DESTINATION "${CMAKE_INSTALL_INCLUDEDIR}/${PROJECT_NAME}"
	)

	install(EXPORT "${PROJECT_NAME}-config" DESTINATION "${CMAKE_INSTALL_LIBDIR}/cmake/${PROJECT_NAME}")

	export(TARGETS ${PROJECT_NAME} FILE "${PROJECT_NAME}-exports.cmake")
endif()
