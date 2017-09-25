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

find_package(Bullet)
find_package(ccd)
find_package(FCL)
find_package(ODE)
find_package(PQP)
find_package(SOLID3)
find_package(SoQt)

if(QT_FOUND AND SoQt_FOUND)
	if(BULLET_FOUND OR (CCD_FOUND AND FCL_FOUND) OR ODE_FOUND OR PQP_FOUND OR SOLID3_FOUND)
		set(
			HDRS
			BodyDelegate.h
			BodyModel.h
			MainWindow.h
		)
		
		set(
			SRCS
			BodyDelegate.cpp
			BodyModel.cpp
			MainWindow.cpp
			rlCollisionDemo.cpp
			${rl_SOURCE_DIR}/robotics-library.rc
		)
		
		add_executable(
			rlCollisionDemo
			${HDRS}
			${SRCS}
		)
		
		target_compile_definitions(
			rlCollisionDemo
			PUBLIC
			${SoQt_DEFINITIONS}
		)
		
		target_include_directories(
			rlCollisionDemo
			PUBLIC
			${SoQt_INCLUDE_DIRS}
		)
		
		target_link_libraries(
			rlCollisionDemo
			sg
			${QT_LIBRARIES}
			${SoQt_LIBRARIES}
		)
		
		set_target_properties(
			rlCollisionDemo
			PROPERTIES
			VERSION ${VERSION}
		)
		
		if(WIN32)
			set_target_properties(
				rlCollisionDemo
				PROPERTIES
				DEBUG_POSTFIX d
			)
		endif()
		
		install(
			TARGETS rlCollisionDemo
			COMPONENT demos
			ARCHIVE DESTINATION ${CMAKE_INSTALL_LIBDIR}
			LIBRARY DESTINATION ${CMAKE_INSTALL_LIBDIR}
			RUNTIME DESTINATION ${CMAKE_INSTALL_BINDIR}
		)
		
		if(MSVC AND BUILD_SHARED_LIBS AND NOT CMAKE_VERSION VERSION_LESS 3.1)
			install(FILES $<TARGET_PDB_FILE:rlCollisionDemo> DESTINATION ${CMAKE_INSTALL_BINDIR} CONFIGURATIONS Debug RelWithDebInfo COMPONENT demos)
		endif()
		
		if(UNIX)
			configure_file(rlCollisionDemo.desktop.in rlCollisionDemo.desktop @ONLY)
			install(FILES ${CMAKE_CURRENT_BINARY_DIR}/rlCollisionDemo.desktop DESTINATION ${CMAKE_INSTALL_DATADIR}/applications COMPONENT demos)
		endif()
		
		set(
			CPACK_NSIS_CREATE_ICONS_EXTRA
			${CPACK_NSIS_CREATE_ICONS_EXTRA}
			"CreateShortCut \\\\
				\\\"$SMPROGRAMS\\\\$STARTMENU_FOLDER\\\\rlCollisionDemo.lnk\\\" \\\\
				\\\"$INSTDIR\\\\${CMAKE_INSTALL_BINDIR}\\\\rlCollisionDemo.exe\\\" \\\\
				\\\"\\\\
					\\\$\\\\\\\"$INSTDIR\\\\${CMAKE_INSTALL_DATADIR}\\\\rl-${VERSION}\\\\examples\\\\rlsg\\\\scene.xml\\\$\\\\\\\"\\\\
				\\\" \\\\
				\\\"\\\" \\\"\\\" \\\"\\\" \\\"\\\" \\\\
				\\\"Collision detection and distance computation demo with basic scene\\\""
			PARENT_SCOPE
		)
		set(CPACK_NSIS_DELETE_ICONS_EXTRA ${CPACK_NSIS_DELETE_ICONS_EXTRA} "Delete \\\"$SMPROGRAMS\\\\$START_MENU\\\\rlCollisionDemo.lnk\\\"" PARENT_SCOPE)
	endif()
endif()
