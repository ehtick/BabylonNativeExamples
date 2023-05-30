set(WIN32_EXECUTABLE TRUE)

set(SCRIPTS
    "node_modules/babylonjs/babylon.max.js"
    "node_modules/babylonjs-loaders/babylonjs.loaders.js"
    "Scripts/index.js")

set(SOURCES
    "Win32/App.cpp"
    
    "Win32/App.ico"
    "Win32/App.rc"
    "Win32/Resource.h"
    "Win32/small.ico"
    "Win32/targetver.h")

set(MODELS
    "Models/candy.onnx"
    "Models/la_muse.onnx"
    "Models/mosaic.onnx"
    "Models/udnie.onnx")

add_executable(StyleTransferApp WIN32 ${SCRIPTS} ${SOURCES} ${MODELS})

target_compile_definitions(StyleTransferApp PRIVATE UNICODE)
target_compile_definitions(StyleTransferApp PRIVATE _UNICODE)

target_compile_options(StyleTransferApp PRIVATE /await)

target_link_to_dependencies(StyleTransferApp
    PRIVATE AppRuntime
    PRIVATE Console
    PRIVATE ExternalTexture
    PRIVATE NativeEngine
    PRIVATE NativeInput
    PRIVATE Canvas
    PRIVATE ScriptLoader
    PRIVATE Window
    PRIVATE XMLHttpRequest)

foreach(SCRIPT ${SCRIPTS})
    get_filename_component(SCRIPT_NAME "${SCRIPT}" NAME)
    add_custom_command(
        OUTPUT "${CMAKE_CFG_INTDIR}/Scripts/${SCRIPT_NAME}"
        COMMAND "${CMAKE_COMMAND}" -E copy "${CMAKE_CURRENT_SOURCE_DIR}/${SCRIPT}" "${CMAKE_CURRENT_BINARY_DIR}/${CMAKE_CFG_INTDIR}/Scripts/${SCRIPT_NAME}"
        COMMENT "Copying ${SCRIPT_NAME}"
        MAIN_DEPENDENCY "${CMAKE_CURRENT_SOURCE_DIR}/${SCRIPT}")
endforeach()

foreach(MODEL ${MODELS})
    get_filename_component(MODEL_NAME "${MODEL}" NAME)
    add_custom_command(
        OUTPUT "${CMAKE_CFG_INTDIR}/Models/${MODEL_NAME}"
        COMMAND "${CMAKE_COMMAND}" -E copy "${CMAKE_CURRENT_SOURCE_DIR}/${MODEL}" "${CMAKE_CURRENT_BINARY_DIR}/${CMAKE_CFG_INTDIR}/Models/${MODEL_NAME}"
        COMMENT "Copying ${MODEL_NAME}"
        MAIN_DEPENDENCY "${CMAKE_CURRENT_SOURCE_DIR}/${MODEL}")
endforeach()

set_property(TARGET StyleTransferApp PROPERTY FOLDER Apps)
source_group(TREE ${CMAKE_CURRENT_SOURCE_DIR} FILES ${SCRIPTS})
source_group(TREE ${CMAKE_CURRENT_SOURCE_DIR} FILES ${SOURCES})
source_group(TREE ${CMAKE_CURRENT_SOURCE_DIR} FILES ${MODELS})
set_property(DIRECTORY ${CMAKE_SOURCE_DIR} PROPERTY VS_STARTUP_PROJECT StyleTransferApp)