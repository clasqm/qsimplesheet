cmake_minimum_required(VERSION 3.16)

project(QSimpleSheet VERSION 0.3.9 LANGUAGES CXX)

set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
set(CMAKE_CXX_EXTENSIONS OFF)

set(CMAKE_AUTOMOC ON)
set(CMAKE_AUTORCC ON)
set(CMAKE_AUTOUIC ON)

find_package(Qt5 REQUIRED COMPONENTS Widgets)

set(QSimpleSheet_SRCS
    src/Calculator.cpp
    src/Cell.cpp
    src/Commands.cpp
    src/Graphic.cpp
    src/ItemDelegate.cpp
    src/MainWindow.cpp
    src/PictureGraphic.cpp
    src/QSimplesheet.cpp
    src/Table.cpp
    src/Dialogs/FindDialog.cpp
    src/Dialogs/FunctionListDialog.cpp
    src/Dialogs/GraphicPropertiesDialog.cpp
    src/Dialogs/InsertRCDialog.cpp
    src/Dialogs/MySpinBox.cpp
    src/Dialogs/NewFileDialog.cpp
    src/Dialogs/SortDialog.cpp
)

set(QSimpleSheet_UIS
    src/Dialogs/FindDialog.ui
    src/Dialogs/FunctionListDialog.ui
    src/Dialogs/GraphicPropertiesDialog.ui
    src/Dialogs/InsertRCDialog.ui
    src/Dialogs/NewFileDialog.ui
    src/Dialogs/SortDialog.ui
)

add_executable(qsimplesheet
    ${QSimpleSheet_SRCS}
    ${QSimpleSheet_UIS}
    src/resources.qrc
)

target_include_directories(qsimplesheet PRIVATE
    ${CMAKE_CURRENT_SOURCE_DIR}/src
    ${CMAKE_CURRENT_SOURCE_DIR}/src/Dialogs
)

target_link_libraries(qsimplesheet PRIVATE Qt5::Widgets)

if(UNIX AND NOT APPLE)
    if(NOT DESKTOP_ENTRY)
        set(DESKTOP_ENTRY qsimplesheet.desktop)
    endif()

    add_custom_command(
        OUTPUT ${DESKTOP_ENTRY}
        COMMAND ${CMAKE_COMMAND} -E touch ${DESKTOP_ENTRY}
        COMMAND sh ${PROJECT_SOURCE_DIR}/qsimplesheet-desktop.sh ${CMAKE_INSTALL_PREFIX} > ${DESKTOP_ENTRY}
        DEPENDS ${PROJECT_SOURCE_DIR}/qsimplesheet-desktop.sh
        COMMENT "Generating desktop entry file"
    )

    add_custom_target(DESKTOP_ENTRY_FILE ALL DEPENDS ${DESKTOP_ENTRY})
    set(APP_ICON src/images/applicationIcon.png)
endif()

if(NOT DESKTOP_ENTRY_PATH)
    set(DESKTOP_ENTRY_PATH ${CMAKE_INSTALL_PREFIX}/share/applications)
endif()

if(UNIX AND NOT APPLE)
    install(FILES ${APP_ICON} DESTINATION share/icons)
    install(FILES ${QSimpleSheet_BINARY_DIR}/${DESKTOP_ENTRY} DESTINATION ${DESKTOP_ENTRY_PATH})
endif()

install(TARGETS qsimplesheet DESTINATION bin)
