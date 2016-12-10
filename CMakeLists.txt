cmake_minimum_required(VERSION 3.7)

set(CMAKE_MACOSX_BUNDLE TRUE)

project(sample C)

set(CMAKE_OSX_SYSROOT iphoneos)
set(CMAKE_OSX_ARCHITECTURES "armv7")
set(CMAKE_XCODE_ATTRIBUTE_CODE_SIGNING_REQUIRED "NO")

if(NOT XCODE)
  message(FATAL_ERROR "Xcode Generator required")
endif()

# --- Message Extension

set(RESOURCES
    MessagesExtension/Assets.xcassets
    MessagesExtension/Base.lproj/MainInterface.storyboard
)

add_library(MessagesExtension SHARED
    MessagesExtension/MessagesViewController.m
    MessagesExtension/MessagesViewController.h
    ${RESOURCES}
)

set_target_properties(MessagesExtension PROPERTIES
    FRAMEWORK TRUE
    FRAMEWORK_EXTENSION "appex"
    RESOURCE "${RESOURCES}"
    FRAMEWORK_BUNDLE_INFO_PLIST "${PROJECT_SOURCE_DIR}/MessagesExtension/Info.plist"
    XCODE_EXPLICIT_FILE_TYPE "wrapper.app-extension"
    XCODE_PRODUCT_TYPE "com.apple.product-type.app-extension.messages"
)

target_link_libraries(MessagesExtension PRIVATE "-framework Messages")

# --- Sample App

set(RESOURCES
    sample/Assets.xcassets
)

add_executable(sample
    dummy.c
    ${RESOURCES}
)

#;../foo.txt;$<TARGET_FILE:MessagesExtension>

set_target_properties(sample PROPERTIES
    MACOSX_BUNDLE TRUE
    RESOURCE "${RESOURCES}"
    MACOSX_BUNDLE_INFO_PLIST "${PROJECT_SOURCE_DIR}/sample/Info.plist"
    XCODE_PRODUCT_TYPE "com.apple.product-type.application.messages"
)

target_link_libraries(sample MessagesExtension)
