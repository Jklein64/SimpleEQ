cmake_minimum_required(VERSION 3.15)

project(SimpleEQ VERSION 0.0.1)

# since installed with submodule
add_subdirectory(juce)

juce_add_plugin(SimpleEQ
    PLUGIN_MANUFACTURER_CODE Juce
    PLUGIN_CODE SpEQ
    
    BUNDLE_ID com.jklein.SimpleEQ
    MANUFACTURER_NAME "Jason Klein"
    COMPANY_NAME "Jason Klein"
    
    # for debugging 
    COPY_PLUGIN_AFTER_BUILD TRUE
    
    FORMATS AU VST3 Standalone
    PRODUCT_NAME "Simple EQ")

target_sources(SimpleEQ
    PRIVATE
        PluginEditor.cpp
        PluginProcessor.cpp)

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
    PUBLIC
        juce::juce_recommended_config_flags
        juce::juce_recommended_lto_flags
        juce::juce_recommended_warning_flags)
