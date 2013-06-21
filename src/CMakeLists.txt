set(WHISKERMENU_FLAGS "-Wall -Wextra -Werror")
set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} ${WHISKERMENU_FLAGS}")
set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -fvisibility=hidden -std=c++11 ${WHISKERMENU_FLAGS}")
set(CMAKE_SHARED_LINKER_FLAGS "-Wl,--as-needed")

find_package(GTK2 REQUIRED)
find_package(PkgConfig REQUIRED)
pkg_check_modules(exo REQUIRED exo-1)
pkg_check_modules(garcon REQUIRED garcon-1)
pkg_check_modules(libxfce4panel REQUIRED libxfce4panel-1.0>=4.8)
pkg_check_modules(libxfce4ui REQUIRED libxfce4ui-1>=4.8)
pkg_check_modules(libxfce4util REQUIRED libxfce4util-1.0>=4.8)

if (NOT DEFINED DISABLE_DEPRECATED)
    if(${libxfce4panel_VERSION} VERSION_LESS "4.10")
        set(DISABLE_DEPRECATED OFF)
    else()
        set(DISABLE_DEPRECATED ON)
    endif()
endif()

include_directories(
    ${exo_INCLUDE_DIRS}
    ${garcon_INCLUDE_DIRS}
    ${libxfce4panel_INCLUDE_DIRS}
    ${libxfce4ui_INCLUDE_DIRS}
    ${libxfce4util_INCLUDE_DIRS}
    ${GTK2_INCLUDE_DIRS})

link_directories(
    ${exo_LIBRARY_DIRS}
    ${garcon_LIBRARY_DIRS}
    ${libxfce4panel_LIBRARY_DIRS}
    ${libxfce4ui_LIBRARY_DIRS}
    ${libxfce4util_LIBRARY_DIRS})

add_definitions(
    -DGETTEXT_PACKAGE="whiskermenu"
    -DLOCALEDIR="${CMAKE_INSTALL_PREFIX}/${LOCALE_INSTALL_DIR}"
    -DGSEAL_ENABLE=1
    -DGDK_DISABLE_SINGLE_INCLUDES=1
    -DGDK_PIXBUF_DISABLE_SINGLE_INCLUDES=1
    -DGTK_DISABLE_SINGLE_INCLUDES=1
    -DGTK_MULTIHEAD_SAFE=1
    ${exo_CFLAGS_OTHER}
    ${garcon_CFLAGS_OTHER}
    ${libxfce4panel_CFLAGS_OTHER}
    ${libxfce4ui_CFLAGS_OTHER}
    ${libxfce4util_CFLAGS_OTHER})

if(${DISABLE_DEPRECATED})
    add_definitions(
        -DG_DISABLE_DEPRECATED=1
        -DGDK_DISABLE_DEPRECATED=1
        -DGDK_PIXBUF_DISABLE_DEPRECATED=1
        -DGTK_DISABLE_DEPRECATED=1)
endif()

add_library(whiskermenu SHARED
    applications_page.cpp
    configuration_dialog.cpp
    favorites_page.cpp
    filter_page.cpp
    launcher.cpp
    launcher_model.cpp
    launcher_view.cpp
    list_page.cpp
    menu.cpp
    page.cpp
    panel_plugin.cpp
    recent_page.cpp
    register_plugin.c
    resizer_widget.cpp
    search_page.cpp
    section_button.cpp)

target_link_libraries(whiskermenu
    ${exo_LIBRARIES}
    ${garcon_LIBRARIES}
    ${libxfce4panel_LIBRARIES}
    ${libxfce4ui_LIBRARIES}
    ${libxfce4util_LIBRARIES}
    ${GTK2_LIBRARIES})

install(TARGETS whiskermenu LIBRARY DESTINATION ${LIB_INSTALL_DIR}/xfce4/panel/plugins)
install(FILES ../whiskermenu.desktop DESTINATION ${DATA_INSTALL_DIR}/xfce4/panel/plugins)