cmake_minimum_required(VERSION 2.8)

include(CryptoNoteWallet.cmake)

project(${CN_PROJECT_NAME})

execute_process(COMMAND git log -1 --pretty=format:%h
WORKING_DIRECTORY ${CMAKE_SOURCE_DIR}
OUTPUT_VARIABLE GIT_REVISION)

set(CRYPTONOTE_LIB cycluscoin)

include_directories(${CMAKE_BINARY_DIR}
    src
    cycluscoin/external
    cycluscoin/include
    cycluscoin/src)

find_package(Qt5Gui REQUIRED)
find_package(Qt5Widgets REQUIRED)

set(Boost_USE_STATIC_LIBS ON)
if(WIN32)
  set(Boost_USE_STATIC_RUNTIME OFF)
else(WIN32)
  set(Boost_USE_STATIC_RUNTIME ON)
endif(WIN32)

find_package(Boost 1.55 REQUIRED COMPONENTS date_time filesystem program_options regex serialization system thread chrono)
if ((${Boost_MAJOR_VERSION} EQUAL 1) AND (${Boost_MINOR_VERSION} EQUAL 54))
	message(SEND_ERROR "Boost version 1.54 is unsupported, more details are available here http://goo.gl/RrCFmA")
endif ()

include_directories(${Boost_INCLUDE_DIRS})
link_directories(${Boost_LIBRARY_DIRS})

set(VERSION "")
configure_file("cycluscoin/src/version.h.in" "version.h")
configure_file("src/CryptoNoteWalletConfig.h.in" "CryptoNoteWalletConfig.h")

add_definitions(-DGIT_REVISION=\"${GIT_REVISION}\")

set(CMAKE_AUTOMOC ON)

set(CRYPTONOTE_SOURCES
  cycluscoin/external/miniupnpc/connecthostport.c
  cycluscoin/external/miniupnpc/igd_desc_parse.c
  cycluscoin/external/miniupnpc/minisoap.c
  cycluscoin/external/miniupnpc/miniupnpc.c
  cycluscoin/external/miniupnpc/miniwget.c
  cycluscoin/external/miniupnpc/minixml.c
  cycluscoin/external/miniupnpc/portlistingparse.c
  cycluscoin/external/miniupnpc/receivedata.c
  cycluscoin/external/miniupnpc/upnpcommands.c
  cycluscoin/external/miniupnpc/upnpreplyparse.c
  cycluscoin/src/Common/Base58.cpp
  cycluscoin/src/Common/CommandLine.cpp
  cycluscoin/src/Common/Util.cpp
  cycluscoin/src/Common/StringTools.cpp
  cycluscoin/src/Common/JsonValue.cpp
  cycluscoin/src/Common/ConsoleTools.cpp
  cycluscoin/src/Common/MemoryInputStream.cpp
  cycluscoin/src/Common/PathTools.cpp
  cycluscoin/src/Common/StdInputStream.cpp
  cycluscoin/src/Common/StdOutputStream.cpp
  cycluscoin/src/Common/StreamTools.cpp
  cycluscoin/src/Common/StringOutputStream.cpp
  cycluscoin/src/Common/StringView.cpp
  cycluscoin/src/Common/VectorOutputStream.cpp
  cycluscoin/src/crypto/blake256.c
  cycluscoin/src/crypto/chacha8.c
  cycluscoin/src/crypto/crypto-ops-data.c
  cycluscoin/src/crypto/crypto-ops.c
  cycluscoin/src/crypto/crypto.cpp
  cycluscoin/src/crypto/groestl.c
  cycluscoin/src/crypto/hash-extra-blake.c
  cycluscoin/src/crypto/hash-extra-groestl.c
  cycluscoin/src/crypto/hash-extra-jh.c
  cycluscoin/src/crypto/hash-extra-skein.c
  cycluscoin/src/crypto/hash.c
  cycluscoin/src/crypto/jh.c
  cycluscoin/src/crypto/keccak.c
  cycluscoin/src/crypto/oaes_lib.c
  cycluscoin/src/crypto/random.c
  cycluscoin/src/crypto/skein.c
  cycluscoin/src/crypto/slow-hash.c
  cycluscoin/src/crypto/slow-hash.cpp
  cycluscoin/src/crypto/tree-hash.c
  cycluscoin/src/CryptoNoteCore/BlockchainIndices.cpp
  cycluscoin/src/CryptoNoteCore/BlockchainMessages.cpp
  cycluscoin/src/CryptoNoteCore/BlockIndex.cpp
  cycluscoin/src/CryptoNoteCore/CoreConfig.cpp
  cycluscoin/src/CryptoNoteCore/CryptoNoteBasic.cpp
  cycluscoin/src/CryptoNoteCore/CryptoNoteTools.cpp
  cycluscoin/src/CryptoNoteCore/Currency.cpp
  cycluscoin/src/CryptoNoteCore/MinerConfig.cpp
  cycluscoin/src/CryptoNoteCore/Transaction.cpp
  cycluscoin/src/CryptoNoteCore/Account.cpp
  cycluscoin/src/CryptoNoteCore/Blockchain.cpp
  cycluscoin/src/CryptoNoteCore/Checkpoints.cpp
  cycluscoin/src/CryptoNoteCore/CryptoNoteBasicImpl.cpp
  cycluscoin/src/CryptoNoteCore/Core.cpp
  cycluscoin/src/CryptoNoteCore/CryptoNoteFormatUtils.cpp
  cycluscoin/src/CryptoNoteCore/CryptoNoteSerialization.cpp
  cycluscoin/src/CryptoNoteCore/Difficulty.cpp
  cycluscoin/src/CryptoNoteCore/IBlock.cpp
  cycluscoin/src/CryptoNoteCore/Miner.cpp
  cycluscoin/src/CryptoNoteCore/TransactionExtra.cpp
  cycluscoin/src/CryptoNoteCore/TransactionPool.cpp
  cycluscoin/src/CryptoNoteCore/TransactionPrefixImpl.cpp
  cycluscoin/src/CryptoNoteCore/TransactionUtils.cpp
  cycluscoin/src/InProcessNode/InProcessNode.cpp
  cycluscoin/src/InProcessNode/InProcessNodeErrors.cpp
  cycluscoin/src/NodeRpcProxy/NodeErrors.cpp
  cycluscoin/src/NodeRpcProxy/NodeRpcProxy.cpp
  cycluscoin/src/P2p/NetNodeConfig.cpp
  cycluscoin/src/Serialization/BinaryInputStreamSerializer.cpp
  cycluscoin/src/Serialization/BinaryOutputStreamSerializer.cpp
  cycluscoin/src/Serialization/JsonInputValueSerializer.cpp
  cycluscoin/src/Serialization/JsonOutputStreamSerializer.cpp
  cycluscoin/src/Serialization/KVBinaryInputStreamSerializer.cpp
  cycluscoin/src/Serialization/KVBinaryOutputStreamSerializer.cpp
  cycluscoin/src/Serialization/SerializationOverloads.cpp
  cycluscoin/src/Transfers/BlockchainSynchronizer.cpp
  cycluscoin/src/Transfers/SynchronizationState.cpp
  cycluscoin/src/Transfers/TransfersConsumer.cpp
  cycluscoin/src/Transfers/TransfersContainer.cpp
  cycluscoin/src/Transfers/TransfersSubscription.cpp
  cycluscoin/src/Transfers/TransfersSynchronizer.cpp
  cycluscoin/src/Wallet/LegacyKeysImporter.cpp
  cycluscoin/src/Wallet/WalletAsyncContextCounter.cpp
  cycluscoin/src/Wallet/WalletErrors.cpp
  cycluscoin/src/WalletLegacy/KeysStorage.cpp
  cycluscoin/src/WalletLegacy/WalletLegacy.cpp
  cycluscoin/src/WalletLegacy/WalletHelper.cpp
  cycluscoin/src/WalletLegacy/WalletLegacySerializer.cpp
  cycluscoin/src/WalletLegacy/WalletLegacySerialization.cpp
  cycluscoin/src/WalletLegacy/WalletTransactionSender.cpp
  cycluscoin/src/WalletLegacy/WalletUnconfirmedTransactions.cpp
  cycluscoin/src/WalletLegacy/WalletUserTransactionsCache.cpp
  cycluscoin/src/System/ContextGroup.cpp
  cycluscoin/src/System/Event.cpp
  cycluscoin/src/System/EventLock.cpp
  cycluscoin/src/System/InterruptedException.cpp
  cycluscoin/src/System/Ipv4Address.cpp
  cycluscoin/src/System/TcpStream.cpp
  cycluscoin/src/HTTP/HttpRequest.cpp
  cycluscoin/src/HTTP/HttpParser.cpp
  cycluscoin/src/HTTP/HttpParserErrorCodes.cpp
  cycluscoin/src/HTTP/HttpResponse.cpp
  cycluscoin/src/Rpc/HttpClient.cpp
  cycluscoin/src/Rpc/JsonRpc.cpp
  cycluscoin/src/P2p/NetNode.cpp
  cycluscoin/src/P2p/LevinProtocol.cpp
  cycluscoin/src/P2p/NetNodeConfig.cpp
  cycluscoin/src/P2p/PeerListManager.cpp
  cycluscoin/src/CryptoNoteProtocol/CryptoNoteProtocolHandler.cpp
  cycluscoin/src/Logging/ILogger.cpp
  cycluscoin/src/Logging/LoggerGroup.cpp
  cycluscoin/src/Logging/CommonLogger.cpp
  cycluscoin/src/Logging/LoggerManager.cpp
  cycluscoin/src/Logging/FileLogger.cpp
  cycluscoin/src/Logging/StreamLogger.cpp
  cycluscoin/src/Logging/ConsoleLogger.cpp
  cycluscoin/src/Logging/LoggerMessage.cpp
  cycluscoin/src/Logging/LoggerRef.cpp
  cycluscoin/src/BlockchainExplorer/BlockchainExplorer.cpp
  cycluscoin/src/BlockchainExplorer/BlockchainExplorerDataBuilder.cpp
  cycluscoin/src/BlockchainExplorer/BlockchainExplorerErrors.cpp
)

file(GLOB_RECURSE SOURCES src/*.cpp)
file(GLOB_RECURSE HEADERS src/*.h)
file(GLOB_RECURSE FORMS src/gui/ui/*.ui)

set(QRC src/resources.qrc)

qt5_wrap_ui(UIS ${FORMS})
qt5_add_resources(RCC ${QRC})


if (WIN32)
  if (NOT MSVC)
	message(FATAL_ERROR "Only MSVC is supported on this platform")
  endif ()
  add_definitions(/D_CRT_SECURE_NO_WARNINGS /D_WIN32_WINNT=0x0600 /DSTATICLIB)
  include_directories(cycluscoin/src/platform/msc)

  set(PLATFORM_DIR Windows)
  set(BUILD_PLATFORM WIN32)
  set(BUILD_RESOURCES src/cryptonotewallet.rc)
  set(QTMAIN Qt5::WinMain)

elseif (UNIX)
  set(CRYPTONOTE_SOURCES ${CRYPTONOTE_SOURCES} cycluscoin/external/miniupnpc/minissdpc.c)
  if (APPLE)
    enable_language(ASM)
    file(GLOB_RECURSE OBJC_SOURCES src/*.mm)
    set(SOURCES ${SOURCES} ${OBJC_SOURCES})
    set(PLATFORM_DIR OSX)
    set(MACOSX_BUNDLE_INFO_STRING "CycLusCoin GUI wallet")
    set(MACOSX_BUNDLE_LONG_VERSION_STRING "${VERSION_VERSION}.${VERSION_MINOR}.${VERSION_PATCH}")
    set(MACOSX_BUNDLE_BUNDLE_NAME CryptonoteWallet)
    set(MACOSX_BUNDLE_SHORT_VERSION_STRING "${VERSION_VERSION}.${VERSION_MINOR}.${VERSION_PATCH}")
    set(MACOSX_BUNDLE_BUNDLE_VERSION "$${VERSION_VERSION}.${VERSION_MINOR}.${VERSION_PATCH}")

    find_package(Qt5PrintSupport REQUIRED)

    include_directories(/usr/include/malloc)

    set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -maes -std=c++11 -stdlib=libc++")
    set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -std=c11 -maes -D_DARWIN_C_SOURCE")
    set(CMAKE_EXE_LINKER_FLAGS "${CMAKE_EXE_LINKER_FLAGS} -framework Cocoa -framework OpenGL -framework CoreFoundation -framework Carbon -framework IOKit -L/usr/lib")

    set(MACOSX_BUNDLE_ICON_FILE cycluscoin.icns)
    set(APPLICATION_ICON src/images/cycluscoin.icns)
    set_source_files_properties(${APPLICATION_ICON} PROPERTIES MACOSX_PACKAGE_LOCATION "Resources")

    set(BUILD_PLATFORM MACOSX_BUNDLE)
    set(BUILD_RESOURCES ${APPLICATION_ICON})
	
    GET_TARGET_PROPERTY(QT_LIB_DIR "${Qt5Widgets_LIBRARIES}" LOCATION)
    GET_FILENAME_COMPONENT(QT_LIB_DIR "${QT_LIB_DIR}" PATH)
  else(APPLE)
    set(PLATFORM_DIR Linux)
    set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -maes -std=c++11")
    set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -maes -std=c11")
  endif (APPLE)
endif ()

include_directories(cycluscoin/src/Platform/${PLATFORM_DIR})
file(GLOB PLATFORM_SOURCES cycluscoin/src/Platform/${PLATFORM_DIR}/System/*)
set(CRYPTONOTE_SOURCES ${CRYPTONOTE_SOURCES} ${PLATFORM_SOURCES})

add_library(${CRYPTONOTE_LIB} STATIC ${CRYPTONOTE_SOURCES})
set_target_properties(${CRYPTONOTE_LIB} PROPERTIES COMPILE_DEFINITIONS _GNU_SOURCE)
target_link_libraries(${CRYPTONOTE_LIB} ${Boost_LIBRARIES})

add_executable(${PROJECT_NAME} ${BUILD_PLATFORM} ${BUILD_RESOURCES} ${SOURCES} ${HEADERS} ${UIS} ${RCC})
set_target_properties(${PROJECT_NAME} PROPERTIES COMPILE_DEFINITIONS _GNU_SOURCE)
target_link_libraries(${PROJECT_NAME} ${Boost_LIBRARIES} ${QTMAIN} ${CRYPTONOTE_LIB})

if (APPLE)
  qt5_use_modules(${PROJECT_NAME} PrintSupport)
elseif (UNIX)
  target_link_libraries(${PROJECT_NAME} -lpthread)
elseif (WIN32)
  target_link_libraries(${PROJECT_NAME} Imm32 Iphlpapi Winmm)
endif (APPLE)

qt5_use_modules(${PROJECT_NAME} Widgets Gui)
