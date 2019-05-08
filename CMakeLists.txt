cmake_minimum_required(VERSION 3.3 FATAL_ERROR)
project( Model_Loader )
include(CPack)

foreach(p
    CMP0071 # 3.10: Let AUTOMOC and AUTOUIC process GENERATED files
    )
  if(POLICY ${p})
    cmake_policy(SET ${p} NEW)
  endif()
endforeach()



set( CMAKE_INCLUDE_CURRENT_DIR ON )



include_directories( include )


set( CMAKE_AUTOMOC ON )
set( CMAKE_AUTOUIC ON )

find_package( Qt5Widgets )

set( UIS include/mainwindow.ui)
qt5_wrap_ui( UI_Srcs ${UIS} )

find_package( VTK REQUIRED )
include( ${VTK_USE_FILE} )
# Create library

# 2 requirements

# output library name and File input for library
add_library( vector STATIC src/Vector.cpp)
add_library( material STATIC src/Material.cpp )
add_library( cell STATIC src/Cell.cpp )
add_library( model STATIC src/Model.cpp )
add_library( hexahedron STATIC src/Hexahedron.cpp )

add_executable(Model_Loader MACOSX_BUNDLE
  include/main.h
  include/Cell.h
  include/Model.h
  include/Material.h
  include/Vector.h
  include/Hexahedron.h
  include/mainwindow.h
  src/mainwindow.cpp
  src/Main.cpp
  ${UI_Srcs}
)



# For link output and library
target_link_libraries( Model_Loader  Qt5::Widgets )

# Tell CMake that the executable depends on the VTK libraries
target_link_libraries( Model_Loader  ${VTK_LIBRARIES} )
target_link_libraries( Model_Loader
								model
								vector
								material
								cell
								hexahedron
								)
# Tell CMake that the executable depends on the Qt::Widget libraries.
set(CMAKE_INSTALL_PREFIX ${CMAKE_BINARY_DIR})



if(WIN32)
    set(CPACK_GENERATOR "NSIS")
else()
    set(CPACK_GENERATOR "ZIP")
endif()

set(CMAKE_INSTALL_UCRT_LIBRARIES ON)

set(CPACK_NSIS_CONTACT "Contact - Group 2 (Ed, Hana, P or Harry)")
set(CPACK_NSIS_PACKAGE_NAME "Model_Loader")
set(CPACK_NSIS_DISPLAY_NAME "Group 2 Model Loader 1.0")
set (CPACK_NSIS_MODIFY_PATH "ON")

set(CPACK_NSIS_EXTRA_INSTALL_COMMANDS "  CreateShortCut '$DESKTOP\\\\ModelLoader.lnk' '$INSTDIR\\\\bin\\\\Model_Loader.exe' ")
set(CPACK_NSIS_CREATE_ICONS_EXTRA
    "CreateShortCut '$SMPROGRAMS\\\\$STARTMENU_FOLDER\\\\ModelLoader.lnk' '$INSTDIR\\\\Model_Loader.exe' '-foo -bar'")
install(TARGETS Model_Loader DESTINATION bin)

set(CPACK_NSIS_EXTRA_INSTALL_COMMANDS "
    CreateShortCut \\\"$DESKTOP\\\\${EWS_APP_NAME}.lnk\\\" \\\"$INSTDIR\\\\bin\\\\${EXE_TARGET_NAME}.exe\\\"
")

set(CPACK_NSIS_EXTRA_UNINSTALL_COMMANDS "
    Delete \\\"$DESKTOP\\\\${EWS_APP_NAME}.lnk\\\"
")

install(DIRECTORY libs/ DESTINATION bin)
install(DIRECTORY Resource/ DESTINATION Resources)
install(DIRECTORY platforms/ DESTINATION bin/platforms)


