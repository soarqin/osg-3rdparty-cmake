PROJECT(freetype C)

SET(TARGET_LIB freetype)


IF(MSVC)
	SET(ftdebug "${FREETYPE_SOURCE_DIR}/builds/windows/ftdebug.c")
ENDIF()

IF(MINGW)
	SET(ftdebug "${FREETYPE_SOURCE_DIR}/builds/windows/ftdebug.c")
ENDIF()


CONFIGURE_FILE(${ftdebug} ftdebug.c @ONLY)

# Create new version of ft2build.h to use in include dir
SET(PUBLIC_FT2BUILD_H "${FREETYPE_SOURCE_DIR}/include/ft2build.h")

SET(FREETYPE_PRIVATE_HEADERS
	${FREETYPE_SOURCE_DIR}/include/ft2build.h
	${FREETYPE_SOURCE_DIR}/include/freetype/config/ftconfig.h
	${FREETYPE_SOURCE_DIR}/include/freetype/config/ftheader.h
	${FREETYPE_SOURCE_DIR}/include/freetype/config/ftmodule.h
	${FREETYPE_SOURCE_DIR}/include/freetype/config/ftoption.h
	${FREETYPE_SOURCE_DIR}/include/freetype/config/ftstdlib.h
)

SET(FREETYPE_SRCS
	${CMAKE_CURRENT_BINARY_DIR}/ftdebug.c
	
	${FREETYPE_SOURCE_DIR}/src/autofit/autofit.c
	${FREETYPE_SOURCE_DIR}/src/base/ftbase.c
	${FREETYPE_SOURCE_DIR}/src/base/ftbbox.c
	${FREETYPE_SOURCE_DIR}/src/base/ftbdf.c
	${FREETYPE_SOURCE_DIR}/src/base/ftbitmap.c
	${FREETYPE_SOURCE_DIR}/src/base/ftcid.c
	${FREETYPE_SOURCE_DIR}/src/base/ftfntfmt.c
	${FREETYPE_SOURCE_DIR}/src/base/ftfstype.c
	${FREETYPE_SOURCE_DIR}/src/base/ftgasp.c
	${FREETYPE_SOURCE_DIR}/src/base/ftglyph.c
	${FREETYPE_SOURCE_DIR}/src/base/ftgxval.c
	${FREETYPE_SOURCE_DIR}/src/base/ftinit.c
	${FREETYPE_SOURCE_DIR}/src/base/ftlcdfil.c
	${FREETYPE_SOURCE_DIR}/src/base/ftmm.c
	${FREETYPE_SOURCE_DIR}/src/base/ftotval.c
	${FREETYPE_SOURCE_DIR}/src/base/ftpatent.c
	${FREETYPE_SOURCE_DIR}/src/base/ftpfr.c
	${FREETYPE_SOURCE_DIR}/src/base/ftstroke.c
	${FREETYPE_SOURCE_DIR}/src/base/ftsynth.c
	${FREETYPE_SOURCE_DIR}/src/base/ftsystem.c
	${FREETYPE_SOURCE_DIR}/src/base/fttype1.c
	${FREETYPE_SOURCE_DIR}/src/base/ftwinfnt.c
	${FREETYPE_SOURCE_DIR}/src/bdf/bdf.c
	${FREETYPE_SOURCE_DIR}/src/bzip2/ftbzip2.c
	${FREETYPE_SOURCE_DIR}/src/cache/ftcache.c
	${FREETYPE_SOURCE_DIR}/src/cff/cff.c
	${FREETYPE_SOURCE_DIR}/src/cid/type1cid.c
	${FREETYPE_SOURCE_DIR}/src/gzip/ftgzip.c
	${FREETYPE_SOURCE_DIR}/src/lzw/ftlzw.c
	${FREETYPE_SOURCE_DIR}/src/pcf/pcf.c
	${FREETYPE_SOURCE_DIR}/src/pfr/pfr.c
	${FREETYPE_SOURCE_DIR}/src/psaux/psaux.c
	${FREETYPE_SOURCE_DIR}/src/pshinter/pshinter
	${FREETYPE_SOURCE_DIR}/src/psnames/psnames.c
	${FREETYPE_SOURCE_DIR}/src/raster/raster.c
	${FREETYPE_SOURCE_DIR}/src/sfnt/sfnt.c
	${FREETYPE_SOURCE_DIR}/src/smooth/smooth.c
	${FREETYPE_SOURCE_DIR}/src/truetype/truetype
	${FREETYPE_SOURCE_DIR}/src/type1/type1.c
	${FREETYPE_SOURCE_DIR}/src/type42/type42.c
	${FREETYPE_SOURCE_DIR}/src/winfonts/winfnt.c
)

# Add all freetype include files
FILE(GLOB FREETYPE_INCLUDE 
	"${FREETYPE_SOURCE_DIR}/include/freetype/*.h"
)

FILE(GLOB FREETYPE_INCLUDE_CONFIG 
	"${FREETYPE_SOURCE_DIR}/include/freetype/config/*.h"
)

FILE(GLOB FREETYPE_INCLUDE_INTERNAL 
	"${FREETYPE_SOURCE_DIR}/include/freetype/internal/*.h"
)

FILE(GLOB FREETYPE_INCLUDE_INTERNAL_SERVICES 
	"${FREETYPE_SOURCE_DIR}/include/freetype/internal/services*.h"
)

# Dependent include files
INCLUDE_DIRECTORIES(${FREETYPE_SOURCE_DIR} ${FREETYPE_SOURCE_DIR}/include)

IF(MSVC)
	set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} /W4 /Za /wd4001 /wd4244 /wd4701")
	set(CMAKE_C_FLAGS_RELEASE "${CMAKE_C_FLAGS_RELEASE} /GF /Gy /Ob1 /MP")
	set(CMAKE_C_FLAGS_DEBUG "${CMAKE_C_FLAGS_DEBUG}")

	IF(CMAKE_BUILD_TYPE STREQUAL "Debug")
		ADD_DEFINITIONS(-D_CRT_SECURE_NO_WARNINGS -DFT2_BUILD_LIBRARY -DFT_DEBUG_LEVEL_ERROR -DFT_DEBUG_LEVEL_TRACE)
		
	ELSE()
		ADD_DEFINITIONS(-D_CRT_SECURE_NO_WARNINGS -DFT2_BUILD_LIBRARY)
	ENDIF()
	SET(CMAKE_DEBUG_POSTFIX "d")
ENDIF(MSVC)

IF (MINGW)
	ADD_DEFINITIONS(-DFT2_BUILD_LIBRARY)
ENDIF(MINGW)

ADD_LIBRARY(${TARGET_LIB} STATIC ${FREETYPE_SRCS} ${FREETYPE_PRIVATE_HEADERS})


# Runtime equals DLL-files
# Archive equals static libraries
# Library equals module libraries
IF(NOT SKIP_INSTALL_LIBRARIES AND NOT SKIP_INSTALL_ALL )
	INSTALL(TARGETS ${TARGET_LIB}
		RUNTIME DESTINATION ${EXECUTABLE_OUTPUT_PATH}
		LIBRARY DESTINATION ${LIBRARY_OUTPUT_PATH}
		ARCHIVE DESTINATION ${ARCHIVE_OUTPUT_PATH})
ENDIF()
IF(NOT SKIP_INSTALL_HEADERS AND NOT SKIP_INSTALL_ALL )
	INSTALL(FILES ${PUBLIC_FT2BUILD_H} DESTINATION ${INCLUDE_OUTPUT_PATH})
	INSTALL(FILES ${FREETYPE_INCLUDE} DESTINATION ${INCLUDE_OUTPUT_PATH}/freetype)
	INSTALL(FILES ${FREETYPE_INCLUDE_CONFIG} DESTINATION ${INCLUDE_OUTPUT_PATH}/freetype/config)
	INSTALL(FILES ${FREETYPE_INCLUDE_INTERNAL} DESTINATION ${INCLUDE_OUTPUT_PATH}/freetype/internal)
	INSTALL(FILES ${FREETYPE_INCLUDE_INTERNAL_SERVICES} DESTINATION ${INCLUDE_OUTPUT_PATH}/freetype/internal/services)
ENDIF()
