cmake_minimum_required(VERSION 3.0)

project(vsyasm)

INCLUDE_DIRECTORIES(
    ${PROJECT_BINARY_DIR}
    ${PROJECT_SOURCE_DIR}/../yasm
    )

ADD_CUSTOM_COMMAND(
    OUTPUT ${PROJECT_BINARY_DIR}/license.c
    COMMAND ${PYTHON_EXECUTABLE} ${PROJECT_SOURCE_DIR}/../yasm/genstring.py
        license_msg
        ${PROJECT_BINARY_DIR}/license.c
        ${PROJECT_SOURCE_DIR}/../../COPYING
    MAIN_DEPENDENCY ${PROJECT_SOURCE_DIR}/../../COPYING
    DEPENDS ${PROJECT_SOURCE_DIR}/../yasm/genstring.py
    )

IF(BUILD_STATIC)
	ADD_EXECUTABLE(vsyasm
			vsyasm.c
			${PROJECT_SOURCE_DIR}/../yasm/yasm-options.c
			${PROJECT_SOURCE_DIR}/../yasm/yasm-plugin.c
			)
			
	TARGET_LINK_LIBRARIES(vsyasm libyasmmodules libyasm)
		
	set (COMPILER_OPTIONS
		"/W3"
		"/nologo"
		"/EHa"
		"/GF-"
		"/GR-"
		"/Oi"
		"/Ob2"
	)

	set (COMPILER_OPTIONS_DEBUG
		"/MTd"
		"/O2"
		"/Zi"
	)

	set (COMPILER_OPTIONS_RELEASE
		"/MT"
	)

	target_compile_options(vsyasm PUBLIC
		"${COMPILER_OPTIONS}"
		"$<$<CONFIG:DEBUG>:${COMPILER_OPTIONS_DEBUG}>"
		"$<$<CONFIG:RELEASE>:${COMPILER_OPTIONS_RELEASE}>"
	)
	
	set_target_properties(vsyasm PROPERTIES COMPILE_DEFINITIONS_DEBUG "DEBUG;_DEBUG")
	set_target_properties(vsyasm PROPERTIES COMPILE_DEFINITIONS_RELEASE "NDEBUG")
ELSE(BUILD_STATIC)
	IF(BUILD_SHARED_LIBS)
		ADD_EXECUTABLE(vsyasm
			vsyasm.c
			${PROJECT_SOURCE_DIR}/../yasm/yasm-options.c
			${PROJECT_SOURCE_DIR}/../yasm/yasm-plugin.c
			)
		TARGET_LINK_LIBRARIES(vsyasm libyasm ${LIBDL})
	ELSE(BUILD_SHARED_LIBS)
		ADD_EXECUTABLE(vsyasm
			vsyasm.c
			${PROJECT_SOURCE_DIR}/../yasm/yasm-options.c
			)
		TARGET_LINK_LIBRARIES(vsyasm libyasmmodules libyasm)
	ENDIF(BUILD_SHARED_LIBS)
ENDIF(BUILD_STATIC)

SET_SOURCE_FILES_PROPERTIES(vsyasm.c PROPERTIES
    OBJECT_DEPENDS ${PROJECT_BINARY_DIR}/license.c
    )

INSTALL(TARGETS vsyasm RUNTIME DESTINATION bin)
