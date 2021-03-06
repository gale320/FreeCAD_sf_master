if(MSVC)
    add_definitions(-DHAVE_ATANH -DHAVE_ASINH -DHAVE_ACOSH)
else(MSVC)
    add_definitions(-DHAVE_LIMITS_H -DHAVE_CONFIG_H)
endif(MSVC)

include_directories(
    ${CMAKE_SOURCE_DIR}/src
    ${CMAKE_CURRENT_BINARY_DIR}
    ${Boost_INCLUDE_DIRS}
    ${COIN3D_INCLUDE_DIR}
    ${PYTHON_INCLUDE_PATH}
    ${QT_INCLUDE_DIR}
    ${OCC_INCLUDE_DIR}
    ${ZLIB_INCLUDE_DIR}
    ${SOQT_INCLUDE_DIR}
    ${XERCESC_INCLUDE_DIR}
)
link_directories(${OCC_LIBRARY_DIR})

set(DrawingGui_LIBS
    Drawing
    FreeCADGui
)


set(DrawingGui_MOC_HDRS
    DrawingView.h
    TaskOrthoViews.h
    TaskDialog.h
)




fc_wrap_cpp(DrawingGui_MOC_SRCS ${DrawingGui_MOC_HDRS})
SOURCE_GROUP("Moc" FILES ${DrawingGui_MOC_SRCS})

qt4_add_resources(DrawingGui_SRCS Resources/Drawing.qrc)

set(DrawingGui_UIC_SRCS
    TaskOrthoViews.ui
)

qt4_wrap_ui(DrawingGui_UIC_HDRS ${DrawingGui_UIC_SRCS})

SET(DrawingGui_SRCS
    ${DrawingGui_SRCS}
    AppDrawingGui.cpp
    AppDrawingGuiPy.cpp
    Command.cpp
    Resources/Drawing.qrc
    PreCompiled.cpp
    PreCompiled.h
    TaskDialog.cpp
    TaskDialog.h
    TaskOrthoViews.ui
    TaskOrthoViews.cpp
    TaskOrthoViews.h
    Workbench.cpp
    Workbench.h
)
SET(DrawingGuiView_SRCS
    DrawingView.cpp
    DrawingView.h
)
SET(DrawingGuiViewProvider_SRCS
    ViewProviderPage.cpp
    ViewProviderPage.h
    ViewProviderView.cpp
    ViewProviderView.h
)

SOURCE_GROUP("Mod" FILES ${DrawingGui_SRCS})
SOURCE_GROUP("SVG-View" FILES ${DrawingGuiView_SRCS})
SOURCE_GROUP("ViewProvider" FILES ${DrawingGuiViewProvider_SRCS})

SET(DrawingGuiTaskDlgs_SRCS
    TaskOrthoViews.ui
)
SOURCE_GROUP("TaskDialogs" FILES ${DrawingGuiTaskDlgs_SRCS})

if(MSVC)
    add_definitions(-D_PreComp_)
    GET_MSVC_PRECOMPILED_SOURCE("PreCompiled.cpp" PCH_SRCS ${DrawingGui_SRCS} ${DrawingGuiView_SRCS} ${DrawingGuiViewProvider_SRCS})
    ADD_MSVC_PRECOMPILED_HEADER("PreCompiled.h" "PreCompiled.cpp" PCH_SRCS)
endif(MSVC)

add_library(DrawingGui SHARED ${DrawingGui_SRCS} ${DrawingGuiView_SRCS} ${DrawingGuiViewProvider_SRCS})
target_link_libraries(DrawingGui ${DrawingGui_LIBS})


fc_target_copy_resource(DrawingGui 
    ${CMAKE_SOURCE_DIR}/src/Mod/Drawing
    ${CMAKE_BINARY_DIR}/Mod/Drawing
    InitGui.py)

if(MSVC)
    set_target_properties(DrawingGui PROPERTIES SUFFIX ".pyd")
    set_target_properties(DrawingGui PROPERTIES DEBUG_OUTPUT_NAME "DrawingGui_d")
    set_target_properties(DrawingGui PROPERTIES RUNTIME_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/Mod/Drawing)
    set_target_properties(DrawingGui PROPERTIES PREFIX "../")
elseif(MINGW)
    set_target_properties(DrawingGui PROPERTIES SUFFIX ".pyd")
    set_target_properties(DrawingGui PROPERTIES DEBUG_OUTPUT_NAME "DrawingGui_d")
    set_target_properties(DrawingGui PROPERTIES RUNTIME_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/Mod/Drawing)
    set_target_properties(DrawingGui PROPERTIES PREFIX "")
else(MSVC)
    set_target_properties(DrawingGui PROPERTIES LIBRARY_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/Mod/Drawing)
    set_target_properties(DrawingGui PROPERTIES PREFIX "")
    set_target_properties(DrawingGui PROPERTIES INSTALL_RPATH ${INSTALL_RPATH})
endif(MSVC)

INSTALL(TARGETS DrawingGui DESTINATION lib)
