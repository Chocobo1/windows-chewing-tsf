project(chewingwrapper LANGUAGES CXX)

if(NOT DEFINED PROJECT_LIBCHEWING OR NOT DEFINED CHEWING_DATA_PREFIX)
	message(FATAL_ERROR "PROJECT_LIBCHEWING and CHEWING_DATA_PREFIX must be provided")
endif()

include_directories(
	${PROJECT_SOURCE_DIR}/include
	${PROJECT_LIBCHEWING}/include
)

add_library(chewingwrapper_static STATIC
	${PROJECT_SOURCE_DIR}/include/chewingwrapper.hpp
	${PROJECT_SOURCE_DIR}/src/chewingwrapper.cpp
)
target_link_libraries(chewingwrapper_static libchewing)

enable_testing()
set(CHEWINGWRAPPER_TEST
	test-chewingwrapper
)

set(CHEWINGWRAPPER_TEST_BIN ${PROJECT_BINARY_DIR}/test)

foreach(target ${CHEWINGWRAPPER_TEST})
	add_executable(${target}
		${PROJECT_SOURCE_DIR}/test/${target}.cpp
	)
	target_link_libraries(${target} chewingwrapper_static)
	set_target_properties(${target} PROPERTIES
		RUNTIME_OUTPUT_DIRECTORY ${CHEWINGWRAPPER_TEST_BIN}
		RUNTIME_OUTPUT_DIRECTORY_DEBUG ${CHEWINGWRAPPER_TEST_BIN}
		RUNTIME_OUTPUT_DIRECTORY_RELEASE ${CHEWINGWRAPPER_TEST_BIN}
		COMPILE_DEFINITIONS
			"CHEWING_PATH=\"${CHEWING_DATA_PREFIX}\";CHEWING_USER_PATH=\"${CHEWINGWRAPPER_TEST_BIN}\""
	)

	add_test(${target} ${CHEWINGWRAPPER_TEST_BIN}/${target})
endforeach()
