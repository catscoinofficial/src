CMAKE_MINIMUM_REQUIRED(VERSION 3.10)

set(CMAKE_INCLUDE_CURRENT_DIR ON)
set(CMAKE_AUTOUIC_SEARCH_PATHS ${CMAKE_CURRENT_SOURCE_DIR}/forms ${CMAKE_CURRENT_SOURCE_DIR}/catscoin/forms ${CMAKE_CURRENT_SOURCE_DIR}/catscoin/settings/forms)
set(CMAKE_AUTOMOC ON)
set(CMAKE_AUTORCC ON)
set(CMAKE_AUTOUIC ON)

if (${CMAKE_SYSTEM_NAME} MATCHES "Linux")
    set(CMAKE_POSITION_INDEPENDENT_CODE ON)
endif ()

find_package(Qrcode)
if (QRCODE_FOUND)
    include_directories ( ${QRCODE_INCLUDE_DIR} )
    link_directories ( ${QRCODE_LIBRARY_DIRS} )
endif()

unset(Qt5Enables)
foreach(comp Widgets Core Gui Network LinguistTools DBus Svg Charts)
    find_package(Qt5${comp})
    set(Qt5Enables "${Qt5Enables};Qt5${comp}_FOUND")
    if(Qt5${comp}_FOUND)
        MESSAGE(STATUS "FOUND QT5${comp}")
        include_directories(${include_directories} ${Qt5${comp}_INCLUDE_DIRS} ${QT_USE_FILE})
        add_definitions(${Qt5${comp}_DEFINITIONS})
        list(APPEND Qt5_LIBRARIES ${Qt5${comp}_LIBRARIES})
    else()
        MESSAGE(WARNING "Cant find Qt5${comp}")
    endif()
endforeach(comp)

if (Qt5DBus_FOUND)
    add_compile_options("-DUSE_DBUS")
endif()

if (Qt5Charts_FOUND)
    add_compile_options("-DUSE_QTCHARTS")
endif()

find_program(MOC_BIN NAMES moc moc-qt5 qt5-moc
        PATHS /usr/lib/qt5/bin /usr/lib/x86_64-linux-gnu/qt5/bin /usr/local/opt/qt/bin /usr/local/opt/qt5/bin
        NO_DEFAULT_PATH
        NO_SYSTEM_ENVIRONMENT_PATH)
if (MOC_BIN)
    MESSAGE(STATUS "FOUND MOC ${MOC_BIN}")

endif ()

# Why isn't this done automatically??
if (${CMAKE_SYSTEM_NAME} MATCHES "Darwin")
    set(CMAKE_AUTOMOC_MOC_OPTIONS "-DQ_OS_MAC")
endif()

file(GLOB LOCAL_QT_HEADERS ${CMAKE_CURRENT_SOURCE_DIR}/*.h)
source_group("QtHeaders" FILES ${LOCAL_QT_HEADERS})

find_package(Protobuf REQUIRED)
if(Protobuf_FOUND)
    message(STATUS "Found Protobuf compiler: ${Protobuf_PROTOC_EXECUTABLE}")
    message(STATUS "Found Protobuf library: ${Protobuf_LIBRARIES}")
endif()
include_directories(${PROTOBUF_INCLUDE_DIRS})
include_directories(${CMAKE_CURRENT_BINARY_DIR})

PROTOBUF_GENERATE_CPP(PROTO_SRCS PROTO_HDRS ${CMAKE_CURRENT_SOURCE_DIR}/paymentrequest.proto)

SET(QT_SOURCES
        ${CMAKE_CURRENT_SOURCE_DIR}/bantablemodel.cpp
        ${CMAKE_CURRENT_SOURCE_DIR}/blockexplorer.cpp
        ${CMAKE_CURRENT_SOURCE_DIR}/bitcoinaddressvalidator.cpp
        ${CMAKE_CURRENT_SOURCE_DIR}/bitcoinamountfield.cpp
        ${CMAKE_CURRENT_SOURCE_DIR}/bitcoinunits.cpp
        ${CMAKE_CURRENT_SOURCE_DIR}/clientmodel.cpp
        ${CMAKE_CURRENT_SOURCE_DIR}/csvmodelwriter.cpp
        ${CMAKE_CURRENT_SOURCE_DIR}/governancepage.cpp
        ${CMAKE_CURRENT_SOURCE_DIR}/guiutil.cpp
        ${CMAKE_CURRENT_SOURCE_DIR}/intro.cpp
        ${CMAKE_CURRENT_SOURCE_DIR}/networkstyle.cpp
        ${CMAKE_CURRENT_SOURCE_DIR}/notificator.cpp
        ${CMAKE_CURRENT_SOURCE_DIR}/optionsdialog.cpp
        ${CMAKE_CURRENT_SOURCE_DIR}/optionsmodel.cpp
        ${CMAKE_CURRENT_SOURCE_DIR}/peertablemodel.cpp
        ${CMAKE_CURRENT_SOURCE_DIR}/platformstyle.cpp
        ${CMAKE_CURRENT_SOURCE_DIR}/qvalidatedlineedit.cpp
        ${CMAKE_CURRENT_SOURCE_DIR}/qvaluecombobox.cpp
        ${CMAKE_CURRENT_SOURCE_DIR}/rpcconsole.cpp
        ${CMAKE_CURRENT_SOURCE_DIR}/splashscreen.cpp
        ${CMAKE_CURRENT_SOURCE_DIR}/trafficgraphwidget.cpp
        ${CMAKE_CURRENT_SOURCE_DIR}/utilitydialog.cpp
        ${CMAKE_CURRENT_SOURCE_DIR}/addressbookpage.cpp
        ${CMAKE_CURRENT_SOURCE_DIR}/addresstablemodel.cpp
        ${CMAKE_CURRENT_SOURCE_DIR}/askpassphrasedialog.cpp
        ${CMAKE_CURRENT_SOURCE_DIR}/coincontroldialog.cpp
        ${CMAKE_CURRENT_SOURCE_DIR}/zcatscontroldialog.cpp
        ${CMAKE_CURRENT_SOURCE_DIR}/coincontroltreewidget.cpp
        ${CMAKE_CURRENT_SOURCE_DIR}/proposalframe.cpp
        ${CMAKE_CURRENT_SOURCE_DIR}/editaddressdialog.cpp
        ${CMAKE_CURRENT_SOURCE_DIR}/openuridialog.cpp
        ${CMAKE_CURRENT_SOURCE_DIR}/paymentrequestplus.cpp
        ${CMAKE_CURRENT_SOURCE_DIR}/paymentserver.cpp
        ${CMAKE_CURRENT_SOURCE_DIR}/recentrequeststablemodel.cpp
        ${CMAKE_CURRENT_SOURCE_DIR}/transactiondesc.cpp
        ${CMAKE_CURRENT_SOURCE_DIR}/transactionfilterproxy.cpp
        ${CMAKE_CURRENT_SOURCE_DIR}/transactionrecord.cpp
        ${CMAKE_CURRENT_SOURCE_DIR}/transactiontablemodel.cpp
        ${CMAKE_CURRENT_SOURCE_DIR}/walletmodel.cpp
        ${CMAKE_CURRENT_SOURCE_DIR}/walletmodeltransaction.cpp
        ${CMAKE_CURRENT_SOURCE_DIR}/zcatscontroldialog.cpp
        ${CMAKE_CURRENT_SOURCE_DIR}/catscoin/catscoingui.cpp
        ${CMAKE_CURRENT_SOURCE_DIR}/catscoin/loadingdialog.cpp
        ${CMAKE_CURRENT_SOURCE_DIR}/catscoin/pwidget.cpp
        ${CMAKE_CURRENT_SOURCE_DIR}/catscoin/guitransactionsutils.cpp
        ${CMAKE_CURRENT_SOURCE_DIR}/catscoin/snackbar.cpp
        ${CMAKE_CURRENT_SOURCE_DIR}/catscoin/navmenuwidget.cpp
        ${CMAKE_CURRENT_SOURCE_DIR}/catscoin/lockunlock.cpp
        ${CMAKE_CURRENT_SOURCE_DIR}/catscoin/receivedialog.cpp
        ${CMAKE_CURRENT_SOURCE_DIR}/catscoin/furabstractlistitemdelegate.cpp
        ${CMAKE_CURRENT_SOURCE_DIR}/catscoin/txviewholder.cpp
        ${CMAKE_CURRENT_SOURCE_DIR}/catscoin/qtutils.cpp
        ${CMAKE_CURRENT_SOURCE_DIR}/catscoin/expandablebutton.cpp
        ${CMAKE_CURRENT_SOURCE_DIR}/catscoin/walletpassworddialog.cpp
        ${CMAKE_CURRENT_SOURCE_DIR}/catscoin/topbar.cpp
        ${CMAKE_CURRENT_SOURCE_DIR}/catscoin/txrow.cpp
        ${CMAKE_CURRENT_SOURCE_DIR}/catscoin/addressfilterproxymodel.cpp
        ${CMAKE_CURRENT_SOURCE_DIR}/catscoin/dashboardwidget.cpp
        ${CMAKE_CURRENT_SOURCE_DIR}/catscoin/coincontrolcatswidget.cpp
        ${CMAKE_CURRENT_SOURCE_DIR}/catscoin/addresslabelrow.cpp
        ${CMAKE_CURRENT_SOURCE_DIR}/catscoin/contactdropdownrow.cpp
        ${CMAKE_CURRENT_SOURCE_DIR}/catscoin/contactsdropdown.cpp
        ${CMAKE_CURRENT_SOURCE_DIR}/catscoin/optionbutton.cpp
        ${CMAKE_CURRENT_SOURCE_DIR}/catscoin/mninfodialog.cpp
        ${CMAKE_CURRENT_SOURCE_DIR}/catscoin/coldstakingmodel.cpp
        ${CMAKE_CURRENT_SOURCE_DIR}/catscoin/mnmodel.cpp
        ${CMAKE_CURRENT_SOURCE_DIR}/catscoin/masternodewizarddialog.cpp
        ${CMAKE_CURRENT_SOURCE_DIR}/catscoin/mnrow.cpp
        ${CMAKE_CURRENT_SOURCE_DIR}/catscoin/masternodeswidget.cpp
        ${CMAKE_CURRENT_SOURCE_DIR}/catscoin/sendchangeaddressdialog.cpp
        ${CMAKE_CURRENT_SOURCE_DIR}/catscoin/sendconfirmdialog.cpp
        ${CMAKE_CURRENT_SOURCE_DIR}/catscoin/sendcustomfeedialog.cpp
        ${CMAKE_CURRENT_SOURCE_DIR}/catscoin/sendchangeaddressdialog.cpp
        ${CMAKE_CURRENT_SOURCE_DIR}/catscoin/sendmultirow.cpp
        ${CMAKE_CURRENT_SOURCE_DIR}/catscoin/send.cpp
        ${CMAKE_CURRENT_SOURCE_DIR}/catscoin/addnewaddressdialog.cpp
        ${CMAKE_CURRENT_SOURCE_DIR}/catscoin/addnewcontactdialog.cpp
        ${CMAKE_CURRENT_SOURCE_DIR}/catscoin/requestdialog.cpp
        ${CMAKE_CURRENT_SOURCE_DIR}/catscoin/receivewidget.cpp
        ${CMAKE_CURRENT_SOURCE_DIR}/catscoin/myaddressrow.cpp
        ${CMAKE_CURRENT_SOURCE_DIR}/catscoin/csrow.cpp
        ${CMAKE_CURRENT_SOURCE_DIR}/catscoin/addressholder.cpp
        ${CMAKE_CURRENT_SOURCE_DIR}/catscoin/tooltipmenu.cpp
        ${CMAKE_CURRENT_SOURCE_DIR}/catscoin/addresseswidget.cpp
        ${CMAKE_CURRENT_SOURCE_DIR}/catscoin/defaultdialog.cpp
        ${CMAKE_CURRENT_SOURCE_DIR}/catscoin/denomgenerationdialog.cpp
        ${CMAKE_CURRENT_SOURCE_DIR}/catscoin/privacywidget.cpp
        ${CMAKE_CURRENT_SOURCE_DIR}/catscoin/coldstakingwidget.cpp
        ${CMAKE_CURRENT_SOURCE_DIR}/catscoin/settings/settingsbackupwallet.cpp
        ${CMAKE_CURRENT_SOURCE_DIR}/catscoin/settings/settingsbittoolwidget.cpp
        ${CMAKE_CURRENT_SOURCE_DIR}/catscoin/settings/settingsconsolewidget.cpp
        ${CMAKE_CURRENT_SOURCE_DIR}/catscoin/settings/settingsdisplayoptionswidget.cpp
        ${CMAKE_CURRENT_SOURCE_DIR}/catscoin/settings/settingsfaqwidget.cpp
        ${CMAKE_CURRENT_SOURCE_DIR}/catscoin/settings/settingsinformationwidget.cpp
        ${CMAKE_CURRENT_SOURCE_DIR}/catscoin/settings/settingsmainoptionswidget.cpp
        ${CMAKE_CURRENT_SOURCE_DIR}/catscoin/settings/settingsmultisenddialog.cpp
        ${CMAKE_CURRENT_SOURCE_DIR}/catscoin/settings/settingsmultisendwidget.cpp
        ${CMAKE_CURRENT_SOURCE_DIR}/catscoin/settings/settingssignmessagewidgets.cpp
        ${CMAKE_CURRENT_SOURCE_DIR}/catscoin/settings/settingswalletoptionswidget.cpp
        ${CMAKE_CURRENT_SOURCE_DIR}/catscoin/settings/settingswalletrepairwidget.cpp
        ${CMAKE_CURRENT_SOURCE_DIR}/catscoin/settings/settingswidget.cpp
        ${CMAKE_CURRENT_SOURCE_DIR}/catscoin/welcomecontentwidget.cpp
        ${CMAKE_CURRENT_SOURCE_DIR}/catscoin/splash.cpp
        )

# Workaround until the old rpcconsole UI window is fully removed
set_property(SOURCE ${CMAKE_CURRENT_SOURCE_DIR}/catscoin/settings/settingsconsolewidget.cpp PROPERTY SKIP_AUTOMOC ON)
execute_process(
        COMMAND ${MOC_BIN} -o moc_settingsconsolewidget.cpp settingsconsolewidget.h
        WORKING_DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR}/catscoin/settings
)

execute_process(
        COMMAND ${MOC_BIN} -o moc_pfborderimage.cpp pfborderimage.h
        WORKING_DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR}/catscoin
)
list(APPEND QT_SOURCES ${CMAKE_CURRENT_SOURCE_DIR}/catscoin/moc_pfborderimage.cpp)

if (${CMAKE_SYSTEM_NAME} MATCHES "Darwin")
    list(APPEND QT_SOURCES ${CMAKE_CURRENT_SOURCE_DIR}/macdockiconhandler.mm)
    list(APPEND QT_SOURCES ${CMAKE_CURRENT_SOURCE_DIR}/macnotificationhandler.mm)
endif()

add_library(qt_stuff STATIC ${BitcoinHeaders} ${QtHeaders} ${QT_SOURCES} ${PROTO_SRCS} ${PROTO_HDRS})
target_include_directories(qt_stuff PUBLIC ${CMAKE_SOURCE_DIR}/src
        ${CMAKE_SOURCE_DIR}/src/leveldb/include
        ${CMAKE_SOURCE_DIR}/src/univalue/include
        ${CMAKE_SOURCE_DIR}/src/secp256k1/include
        ${CMAKE_CURRENT_SOURCE_DIR}
        ${CMAKE_CURRENT_SOURCE_DIR}/catscoin
        ${CMAKE_CURRENT_SOURCE_DIR}/catscoin/settings
        ${OPENSSL_INCLUDE_DIR}
        ${BerkeleyDB_INCLUDE_DIRS}
        )
set_property(TARGET qt_stuff PROPERTY CXX_STANDARD 11)

file(GLOB QT_TRANSLATIONS_FILES ${CMAKE_CURRENT_SOURCE_DIR}/locale/*.ts)

get_filename_component(QM_FILES_BINARY_DIR "${CMAKE_CURRENT_SOURCE_DIR}/locale" ABSOLUTE)
set_source_files_properties(${QT_TRANSLATIONS_FILES} PROPERTIES OUTPUT_LOCATION "${QM_FILES_BINARY_DIR}")

QT5_ADD_TRANSLATION(QM ${QT_TRANSLATIONS_FILES})
add_custom_target(translations_target ALL DEPENDS ${QM})

QT5_ADD_RESOURCES(QRC_RESOURCE catscoin.qrc)
QT5_ADD_RESOURCES(QRC_LOCALE_RESOURCE catscoin_locale.qrc)

add_executable(catscoin-qt catscoin.cpp ${QM} ${QRC_RESOURCE} ${QRC_LOCALE_RESOURCE})
add_dependencies(catscoin-qt translations_target libunivalue libsecp256k1 leveldb leveldb_sse42 memenv)
target_include_directories(catscoin-qt PUBLIC ${ENDIAN_INCLUDES})
target_link_libraries(catscoin-qt
        qt_stuff
        univalue
        SERVER_A UTIL_A WALLET_A CLI_A COMMON_A BITCOIN_CRYPTO_A ZEROCOIN_A
        leveldb leveldb_sse42 memenv secp256k1
        ${BerkeleyDB_LIBRARIES} ${OPENSSL_LIBRARIES} ${Boost_LIBRARIES} ${PROTOBUF_LIBRARIES} ${LIBEVENT_LIB}
        pthread
        )
if (${CMAKE_SYSTEM_NAME} MATCHES "Darwin")
    target_link_libraries(catscoin-qt "-framework Cocoa")
endif()
if(GMP_FOUND)
    target_link_libraries(catscoin-qt ${GMP_LIBRARY})
    target_include_directories(catscoin-qt PUBLIC ${GMP_INCLUDE_DIR})
endif()
if(ZMQ_FOUND)
    target_link_libraries(catscoin-qt ZMQ_A ${ZMQ_LIB})
    target_include_directories(catscoin-qt PUBLIC ${ZMQ_INCLUDE_DIR})
endif()
if (QRCODE_FOUND)
    target_link_libraries(catscoin-qt ${QRCODE_LIB})
    target_include_directories(catscoin-qt PUBLIC ${QRCODE_INCLUDE_DIR})
endif()
if(MINIUPNP_FOUND)
    target_compile_definitions(catscoin-qt PUBLIC "-DSTATICLIB -DMINIUPNP_STATICLIB")
    target_link_libraries(catscoin-qt ${MINIUPNP_LIBRARY})
    target_include_directories(catscoin-qt PUBLIC ${MINIUPNP_INCLUDE_DIR})
endif()

target_link_libraries(catscoin-qt Qt5::Gui Qt5::Core Qt5::Widgets Qt5::Network Qt5::Svg ${QT_LIBRARIES})
if (Qt5DBus_FOUND)
    target_link_libraries(catscoin-qt Qt5::DBus ${QT_LIBRARIES})
endif()
if (Qt5Charts_FOUND)
    target_link_libraries(catscoin-qt Qt5::Charts ${QT_LIBRARIES})
endif()
