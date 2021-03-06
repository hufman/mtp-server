project(mtp-server-translations)

# for dh_translations to extract the domain
# (regarding syntax consistency, see http://pad.lv/1181187)
set (GETTEXT_PACKAGE "mtp-server")

include(FindGettext)

set(DOMAIN mtp-server)
set(POT_FILE ${DOMAIN}.pot)
file(GLOB PO_FILES *.po)
file(GLOB_RECURSE I18N_SRCS RELATIVE ${CMAKE_SOURCE_DIR}
    ${CMAKE_SOURCE_DIR}/server/server.cpp
)

foreach(PO_FILE ${PO_FILES})
    get_filename_component(LANG ${PO_FILE} NAME_WE)
    gettext_process_po_files(${LANG} ALL PO_FILES ${PO_FILE})
    set(INSTALL_DIR ${CMAKE_INSTALL_LOCALEDIR}/${LANG}/LC_MESSAGES)
    install(FILES ${CMAKE_CURRENT_BINARY_DIR}/${LANG}.gmo
            DESTINATION ${INSTALL_DIR}
            RENAME ${DOMAIN}.mo)
endforeach(PO_FILE)

find_program(XGETTEXT_EXECUTABLE xgettext)
if(XGETTEXT_EXECUTABLE)
    add_custom_target(${POT_FILE})
    add_custom_command(TARGET ${POT_FILE}
        COMMAND ${XGETTEXT_EXECUTABLE} --c++ --qt --add-comments=TRANSLATORS --keyword=tr --keyword=tr:1,2 -D ${CMAKE_SOURCE_DIR} -s -p ${CMAKE_CURRENT_SOURCE_DIR} -o ${POT_FILE} ${I18N_SRCS}
    )
    foreach(PO_FILE ${PO_FILES})
       add_custom_command(TARGET ${POT_FILE}
            COMMAND ${GETTEXT_MSGMERGE_EXECUTABLE} ${PO_FILE} ${CMAKE_CURRENT_SOURCE_DIR}/${POT_FILE} -o ${PO_FILE}
       )
    endforeach(PO_FILE)
endif()
