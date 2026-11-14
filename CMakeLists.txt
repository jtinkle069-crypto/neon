cmake_minimum_required(VERSION 3.22)
project(NeonDrip VERSION 1.0.0)

set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

# Bundle the C++ runtime into the plugin on Windows, so it loads on PCs
# that don't have the Visual C++ Redistributable installed.
set(CMAKE_MSVC_RUNTIME_LIBRARY "MultiThreaded$<$<CONFIG:Debug>:Debug>")
set(CMAKE_OSX_DEPLOYMENT_TARGET "10.13" CACHE STRING "")
set(CMAKE_OSX_ARCHITECTURES "arm64;x86_64" CACHE STRING "")

# JUCE is downloaded automatically the first time you configure.
include(FetchContent)
FetchContent_Declare(JUCE
    GIT_REPOSITORY https://github.com/juce-framework/JUCE.git
    GIT_TAG        8.0.4
    GIT_SHALLOW    ON)
FetchContent_MakeAvailable(JUCE)

set(NEONDRIP_SOURCES
    Source/Wavetables.cpp
    Source/PluginProcessor.cpp
    Source/NeonLookAndFeel.cpp
    Source/Widgets.cpp
    Source/DripOverlay.cpp
    Source/PluginEditor.cpp)

# Trial-only build: goes silent 1 hour after first open.
function(neondrip_plugin TARGET PRODUCT CODE TRIAL)
    juce_add_plugin(${TARGET}
        COMPANY_NAME "NeonDrip Audio"
        PLUGIN_MANUFACTURER_CODE Ndrp
        PLUGIN_CODE ${CODE}
        FORMATS VST3 Standalone
        PRODUCT_NAME "${PRODUCT}"
        IS_SYNTH TRUE
        NEEDS_MIDI_INPUT TRUE
        NEEDS_MIDI_OUTPUT FALSE
        IS_MIDI_EFFECT FALSE
        EDITOR_WANTS_KEYBOARD_FOCUS FALSE
        COPY_PLUGIN_AFTER_BUILD FALSE
        VST3_CATEGORIES Instrument Synth)

    target_sources(${TARGET} PRIVATE ${NEONDRIP_SOURCES})

    target_compile_definitions(${TARGET} PUBLIC
        JUCE_WEB_BROWSER=0
        JUCE_USE_CURL=0
        JUCE_VST3_CAN_REPLACE_VST2=0
        NEONDRIP_TRIAL=${TRIAL})

    target_link_libraries(${TARGET}
        PRIVATE
            juce::juce_audio_utils
            juce::juce_dsp
        PUBLIC
            juce::juce_recommended_config_flags
            juce::juce_recommended_lto_flags
            juce::juce_recommended_warning_flags)
endfunction()

neondrip_plugin(NeonDripTrial "Neon Drip Trial" Ndp2 1)
