if(USE_QT5)
	find_package(Qt5 COMPONENTS Core Gui OpenGL Widgets QUIET)
endif()

if(Qt5_FOUND)
	set(QT_LIBRARIES ${Qt5Core_LIBRARIES} ${Qt5Gui_LIBRARIES} ${Qt5OpenGL_LIBRARIES} ${Qt5Widgets_LIBRARIES})
	set(QT_FOUND Qt5_FOUND)
else()
	set(QT_USE_IMPORTED_TARGETS ON)
	find_package(Qt4 COMPONENTS QtCore QtGui QtOpenGL)
	include(${QT_USE_FILE})
endif()

set(CMAKE_AUTOMOC ON)
set(CMAKE_INCLUDE_CURRENT_DIR ON)

find_package(SoQt)

if(QT_FOUND AND SoQt_FOUND)
	add_executable(
		rlViewDemo
		rlViewDemo.cpp
		${rl_SOURCE_DIR}/robotics-library.rc
	)
	
	target_compile_definitions(
		rlViewDemo
		PUBLIC
		${SoQt_DEFINITIONS}
	)
	
	include_directories(
		rlViewDemo
		PUBLIC
		${SoQt_INCLUDE_DIRS}
	)
	
	target_link_libraries(
		rlViewDemo
		sg
		${QT_LIBRARIES}
		${SoQt_LIBRARIES}
	)
	
	set_target_properties(
		rlViewDemo
		PROPERTIES
		VERSION ${VERSION}
	)
	
	if(WIN32)
		set_target_properties(
			rlViewDemo
			PROPERTIES
			DEBUG_POSTFIX d
		)
	endif()
	
	install(
		TARGETS rlViewDemo
		COMPONENT demos
		ARCHIVE DESTINATION ${CMAKE_INSTALL_LIBDIR}
		LIBRARY DESTINATION ${CMAKE_INSTALL_LIBDIR}
		RUNTIME DESTINATION ${CMAKE_INSTALL_BINDIR}
	)
	
	if(MSVC AND BUILD_SHARED_LIBS AND NOT CMAKE_VERSION VERSION_LESS 3.1)
		install(FILES $<TARGET_PDB_FILE:rlViewDemo> DESTINATION ${CMAKE_INSTALL_BINDIR} CONFIGURATIONS Debug RelWithDebInfo COMPONENT demos)
	endif()
	
	if(UNIX)
		configure_file(rlViewDemo.desktop.in rlViewDemo.desktop @ONLY)
		install(FILES ${CMAKE_CURRENT_BINARY_DIR}/rlViewDemo.desktop DESTINATION ${CMAKE_INSTALL_DATADIR}/applications COMPONENT demos)
	endif()
	
	set(
		CPACK_NSIS_CREATE_ICONS_EXTRA
		${CPACK_NSIS_CREATE_ICONS_EXTRA}
		"CreateShortCut \\\\
			\\\"$SMPROGRAMS\\\\$STARTMENU_FOLDER\\\\rlViewDemo.lnk\\\" \\\\
			\\\"$INSTDIR\\\\${CMAKE_INSTALL_BINDIR}\\\\rlViewDemo.exe\\\" \\\\
			\\\"\\\\
				\\\$\\\\\\\"$INSTDIR\\\\${CMAKE_INSTALL_DATADIR}\\\\rl-${VERSION}\\\\examples\\\\rlsg\\\\unimation-puma560_boxes.xml\\\$\\\\\\\"\\\\
			\\\" \\\\
			\\\"\\\" \\\"\\\" \\\"\\\" \\\"\\\" \\\\
			\\\"Visualization demo with Unimation Puma 560\\\""
		PARENT_SCOPE
	)
	set(CPACK_NSIS_DELETE_ICONS_EXTRA ${CPACK_NSIS_DELETE_ICONS_EXTRA} "Delete \\\"$SMPROGRAMS\\\\$START_MENU\\\\rlViewDemo.lnk\\\"" PARENT_SCOPE)
endif()
