cmake_minimum_required(VERSION 3.0)

project(tasm)

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
	ADD_EXECUTABLE(ytasm
			tasm.c
			tasm-options.c
			${PROJECT_SOURCE_DIR}/../yasm/yasm-plugin.c
			)
			
	TARGET_LINK_LIBRARIES(ytasm libyasmmodules libyasm)
		
	set (COMPILER_OPTIONS
		"/W3"
		"/nologo"
		"/EHa"
		"/GF-"
		"/GR-"
		"/Oi"
		"/Ob2"
		"/MT"
	)

	set (COMPILER_OPTIONS_DEBUG
		"/O2"
		"/Zi"
	)

	set (COMPILER_OPTIONS_RELEASE
	)

	target_compile_options(ytasm PUBLIC
		"${COMPILER_OPTIONS}"
		"$<$<CONFIG:DEBUG>:${COMPILER_OPTIONS_DEBUG}>"
		"$<$<CONFIG:RELEASE>:${COMPILER_OPTIONS_RELEASE}>"
	)
	
	set_target_properties(ytasm PROPERTIES COMPILE_DEFINITIONS_DEBUG "DEBUG;_DEBUG")
	set_target_properties(ytasm PROPERTIES COMPILE_DEFINITIONS_RELEASE "NDEBUG")
ELSE(BUILD_STATIC)
	IF(BUILD_SHARED_LIBS)
		ADD_EXECUTABLE(ytasm
			tasm.c
			tasm-options.c
			${PROJECT_SOURCE_DIR}/../yasm/yasm-plugin.c
			)
		TARGET_LINK_LIBRARIES(ytasm libyasmmodules ${LIBDL})
	ELSE(BUILD_SHARED_LIBS)
		ADD_EXECUTABLE(ytasm
			tasm.c
			tasm-options.c
			)
		TARGET_LINK_LIBRARIES(ytasm libyasmmodules libyasm)
	ENDIF(BUILD_SHARED_LIBS)
ENDIF(BUILD_STATIC)

SET_SOURCE_FILES_PROPERTIES(tasm.c PROPERTIES
    OBJECT_DEPENDS ${PROJECT_BINARY_DIR}/license.c
    )

INSTALL(TARGETS ytasm RUNTIME DESTINATION bin)
