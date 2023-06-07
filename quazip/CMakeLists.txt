# set all include directories for in and out of source builds
set(TARGET_NAME QuaZip)

set(CMAKE_AUTOMOC ON)

include_directories(
	${CMAKE_CURRENT_SOURCE_DIR}
	${CMAKE_CURRENT_BINARY_DIR}
	${ZLIB_INCLUDE_DIRS}
)

if(USE_ZLIB_NG)
include_directories(${CMAKE_CURRENT_BINARY_DIR}/../zlib_ng)
endif()

file(GLOB SRCS "*.c" "*.cpp")
file(GLOB PUBLIC_HEADERS "*.h")

# Must be added to enable export macro
ADD_DEFINITIONS(-DQUAZIP_BUILD)


add_library(${QUAZIP_LIB_TARGET_NAME} SHARED ${SRCS})
# add_library(quazip_static STATIC ${SRCS})
 
# Windows uses .lib extension for both static and shared library
# *nix systems use different extensions for SHARED and STATIC library and by convention both libraries have the same name
# if (NOT WIN32)
# 	set_target_properties(quazip_static PROPERTIES OUTPUT_NAME quazip${QUAZIP_LIB_VERSION_SUFFIX})
# endif ()

# Link against ZLIB_LIBRARIES if needed (on Windows this variable is empty)


if(USE_QT5)
	find_package(Qt5 COMPONENTS Core)
	target_link_libraries(${QUAZIP_LIB_TARGET_NAME} ${QT_QTMAIN_LIBRARY} ${QTCORE_LIBRARIES} zlibstatic)
else()
	find_package(Qt6 COMPONENTS Core)
	target_link_libraries(${QUAZIP_LIB_TARGET_NAME} ${QT_QTMAIN_LIBRARY} ${QTCORE_LIBRARIES} Qt6::Core5Compat zlibstatic)
endif()
#sd_link_library(${QUAZIP_LIB_TARGET_NAME} zlibstatic)
#target_link_libraries(quazip_static ${QT_QTMAIN_LIBRARY} ${QTCORE_LIBRARIES} ${ZLIB_LIBRARIES})

#install(FILES ${PUBLIC_HEADERS} DESTINATION include/quazip${QUAZIP_LIB_VERSION_SUFFIX})
#install(TARGETS ${QUAZIP_LIB_TARGET_NAME} quazip_static LIBRARY DESTINATION ${LIB_DESTINATION} ARCHIVE DESTINATION ${LIB_DESTINATION} RUNTIME DESTINATION ${LIB_DESTINATION})
set_target_properties(QuaZip PROPERTIES FOLDER "3rdParty")

install(TARGETS ${TARGET_NAME} RUNTIME  DESTINATION bin)
if(BUILD_AS_LIB)
	install(FILES ${PUBLIC_HEADERS} DESTINATION 3rdParty/QuaZip/quazip)
endif()

if(MSVC)
	install(FILES $<TARGET_PDB_FILE:${TARGET_NAME}> DESTINATION pdb)
endif(MSVC)
