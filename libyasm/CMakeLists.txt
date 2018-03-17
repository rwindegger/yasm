cmake_minimum_required(VERSION 3.0)

PROJECT(libyasm)

file(GLOB Sources "*.c")
file(GLOB Includes "*.h")

IF(BUILD_STATIC)
	add_library(libyasm STATIC ${Sources} ${Includes})
	SET_TARGET_PROPERTIES(libyasm PROPERTIES
		OUTPUT_NAME "libyasm"
		COMPILE_FLAGS -DYASM_LIB_SOURCE
	)
	
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

	target_compile_options(libyasm PUBLIC
		"${COMPILER_OPTIONS}"
		"$<$<CONFIG:DEBUG>:${COMPILER_OPTIONS_DEBUG}>"
		"$<$<CONFIG:RELEASE>:${COMPILER_OPTIONS_RELEASE}>"
	)
	
	set_target_properties(libyasm PROPERTIES COMPILE_DEFINITIONS_DEBUG "DEBUG;_DEBUG")
	set_target_properties(libyasm PROPERTIES COMPILE_DEFINITIONS_RELEASE "NDEBUG")

ELSE(BUILD_STATIC)
	add_library(libyasm ${Sources} ${Includes})
	IF(BUILD_SHARED_LIBS)
		SET_TARGET_PROPERTIES(libyasm PROPERTIES
			OUTPUT_NAME "yasm"
			COMPILE_FLAGS -DYASM_LIB_SOURCE
			)
	ELSE(BUILD_SHARED_LIBS)
		SET_TARGET_PROPERTIES(libyasm PROPERTIES
			COMPILE_FLAGS -DYASM_LIB_SOURCE
			)
	ENDIF(BUILD_SHARED_LIBS)
ENDIF(BUILD_STATIC)

target_include_directories(libyasm PUBLIC ${PROJECT_SOURCE_DIR})
