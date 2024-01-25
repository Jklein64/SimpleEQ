cmake_minimum_required(VERSION 3.15)

project(SimpleEQ VERSION 0.0.1)

# since installed with submodule
add_subdirectory(juce)

set(FORMATS AU VST3 Standalone)

juce_add_plugin(SimpleEQ
    PLUGIN_MANUFACTURER_CODE Juce
    PLUGIN_CODE SpEQ
    
    BUNDLE_ID com.jklein.SimpleEQ
    MANUFACTURER_NAME "Jason Klein"
    COMPANY_NAME "Jason Klein"
    
    # for debugging 
    COPY_PLUGIN_AFTER_BUILD TRUE
    
    FORMATS ${FORMATS}
    PRODUCT_NAME "Simple EQ")

set(SourceFiles
    ./PluginEditor.h
    ./PluginProcessor.h
    ./PluginEditor.cpp
    ./PluginProcessor.cpp)

target_sources(SimpleEQ PRIVATE ${SourceFiles})

target_compile_definitions(SimpleEQ
    PUBLIC
        # JUCE_WEB_BROWSER and JUCE_USE_CURL would be on by default, but you might not need them.
        JUCE_WEB_BROWSER=0  # If you remove this, add `NEEDS_WEB_BROWSER TRUE` to the `juce_add_plugin` call
        JUCE_USE_CURL=0     # If you remove this, add `NEEDS_CURL TRUE` to the `juce_add_plugin` call
        JUCE_VST3_CAN_REPLACE_VST2=0)

target_link_libraries(SimpleEQ
    PRIVATE
        # AudioPluginData           # If we'd created a binary data target, we'd link to it here
        juce::juce_audio_utils
        juce::juce_dsp
    PUBLIC
        juce::juce_recommended_config_flags
        juce::juce_recommended_lto_flags
        juce::juce_recommended_warning_flags)

# No, we don't want our source buried in extra nested folders
set_target_properties(SimpleEQ PROPERTIES FOLDER "")

# The Xcode source tree should uhhh, still look like the source tree, yo
source_group(TREE ${CMAKE_CURRENT_SOURCE_DIR}/ PREFIX "" FILES ${SourceFiles})

# It tucks the Plugin varieties into a "Targets" folder and generate an Xcode Scheme manually
# Xcode scheme generation is turned off globally to limit noise from other targets
# The non-hacky way of doing this is via the global PREDEFINED_TARGETS_FOLDER property
# However that doesn't seem to be working in Xcode
# Not all plugin types (au, vst) available on each build type (win, macos, linux)
foreach (target ${FORMATS} "All")
    if (TARGET ${PROJECT_NAME}_${target})
        set_target_properties(${PROJECT_NAME}_${target} PROPERTIES
            # Tuck the actual plugin targets into a folder where they won't bother us
            FOLDER "Targets"
            # Let us build the target in Xcode
            XCODE_GENERATE_SCHEME ON)
    endif ()
endforeach ()
