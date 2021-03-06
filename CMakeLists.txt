 # project name
    project (com.github.bartzaalberg.alias)

    # the oldest stable cmake version we support
    cmake_minimum_required (VERSION 2.6)

    # tell cmake where its modules can be found in our project directory
    list (APPEND CMAKE_MODULE_PATH ${CMAKE_SOURCE_DIR}/cmake)
    list (APPEND CMAKE_MODULE_PATH ${CMAKE_SOURCE_DIR}/cmake/src)

    # where we install data directory (if we have any)
    set (DATADIR "${CMAKE_INSTALL_PREFIX}/share")

    # what to call that directory where we install data too
    set (PKGDATADIR "${DATADIR}/com.github.bartzaalberg.alias")

    set (EXEC_NAME "com.github.bartzaalberg.alias")
    set (VERSION "0.1.0")
    set (VERSION_INFO "version information")

    set (DEPS_PACKAGES
        gtk+-3.0
        granite
    )

    find_package (PkgConfig)
    pkg_check_modules (DEPS REQUIRED ${DEPS_PACKAGES})


    add_definitions(${DEPS_CFLAGS})
    link_libraries(${DEPS_LIBRARIES})
    link_directories(${DEPS_LIBRARY_DIRS})

    # make sure we have vala
    find_package(Vala REQUIRED)
    # make sure we use vala
    include(ValaVersion)
    # make sure it's the desired version of vala
    ensure_vala_version("0.16" MINIMUM)

    #------------------------------------------------------------------------------------------

    # Translation files
    set (GETTEXT_PACKAGE "${CMAKE_PROJECT_NAME}")
    add_definitions (-DGETTEXT_PACKAGE=\"${GETTEXT_PACKAGE}\")
    add_subdirectory (po)

    #------------------------------------------------------------------------------------------
    
    # files we want to compile
    include(ValaPrecompile)
    vala_precompile(VALA_C ${EXEC_NAME}
        src/Application.vala
        src/MainWindow.vala
        src/ListBoxRow.vala
        src/ListBox.vala
        src/Constants.vala
        src/StackManager.vala
        src/ListManager.vala
        src/ResponseTranslator.vala
        src/FileManager.vala
        src/CommandManager.vala
        src/Alias.vala
        src/Dialogs/Alert.vala
        src/Dialogs/DeleteConfirm.vala
        src/Views/ListView.vala
        src/Views/NotFoundView.vala
        src/Views/WelcomeView.vala
        src/Views/EditView.vala
        src/Views/CreateView.vala
        src/Components/FormComponent.vala
        src/Components/FormLabel.vala
        src/Components/FormEntry.vala
        src/Components/HeaderBar.vala
        src/Components/HeaderLabel.vala

    # tell what libraries to use when compiling
    PACKAGES
        gtk+-3.0
        granite
    )

    include (GResource)
    glib_compile_resources (GLIB_RESOURCES_ICONS SOURCE /data/icons/icons.gresource.xml)

    # tell cmake what to call the executable we just made and install
    add_executable(${EXEC_NAME} ${VALA_C} ${GLIB_RESOURCES_ICONS})

    # install the binaries we just made
    install (TARGETS ${EXEC_NAME} RUNTIME DESTINATION bin)

    #add the data directory and activated the cmakelists in that folder
    add_subdirectory (data)
