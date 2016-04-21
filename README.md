# A convention for Objective-C libraries

The following convention defines a file system structure for a shared Objective-C library.

    LibraryName/
      .clang-format           <- Libraries are expected to use clang-format.
      .jazzy.yaml             <- Libraries should support the Jazzy documentation generator.
      .travis.yml             <- Libraries should support Travis CI.
      
      CHANGELOG.md            <- Release notes and history.
      README.md               <- Essential installation and usage guide.
      LibraryName.podspec     <- The podspec for the library.
    
      docs/                   <- In-depth technical documentation.
        TechnicalDoc1.md      <- Docs are written in Markdown.
        assets/               <- All documentation assets live here.
          image.png           <- Pngs, movs, gifs, etc...
    
      examples/
        Example.swift         <- Examples can be Swift,
        Example.h             <-                        or
        Example.m             <-                           Objective-C.
        supplemental/         <- Non-educational code used by the examples.
          SomeView.swift      <- Supplemental code can be Swift
          SomeView.h          <-                                or
          SomeView.m          <-                                   Objective-C.
        apps/                 <- Example applications live in this sub-directory.
          ExampleApp/         <- Example application.
          AnotherApp/         <- Another example application.
        resources/            <- Resources required by the examples.
    
      src/                    <- All library source lives here.
        LibraryName.h         <- Umbrella header.
        GOSObject.h           <- Library source must be written in Objective-C.
        GOSObject.m           
        private/              <- Private APIs live in a sub-directory
          GOSPrivateAPI.h
          GOSPrivateAPI.m
        LibraryName.bundle/   <- All assets required by the source.
    
      tests/
        interaction/          <- User interaction tests.
          SomeTest.swift      <- Tests can be Swift,
          AnotherTest.m       <-                     or Objective-C.
        unit/                 <- Unit tests.
          SomeTest.swift      <- Tests can be Swift,
          AnotherTest.m       <-                     or Objective-C.

## Style conventions

GOS libraries use clang-format to automatically clean up stylistic aspects of the source. Place a
copy of [.clang-format](.clang-format) at the root of the library tree.

There is a soft (aka: human-enforced) 100 character line length limit.

## Jazzy

Your `.jazzy.yaml` file should contain the following information:

    module: LibraryName
    umbrella_header: src/LibraryName.h
    objc: true
    sdk: iphonesimulator

## Travis CI

Your `.travis.yml` file should contain the following information:

    language: objective-c
    osx_image: xcode7.2
    sudo: false
    notifications:
      email: false
    env:
      global:
      - LC_CTYPE=en_US.UTF-8
      - LANG=en_US.UTF-8
      matrix:
        - DESTINATION="OS=9.2,name=iPhone 6 Plus" SDK=iphonesimulator9.2
        - DESTINATION="OS=9.1,name=iPhone 6s" SDK=iphonesimulator9.2
        - DESTINATION="OS=9.0,name=iPhone 6 Plus" SDK=iphonesimulator9.2
        - DESTINATION="OS=8.4,name=iPhone 6" SDK=iphonesimulator9.2
        - DESTINATION="OS=8.3,name=iPhone 5S" SDK=iphonesimulator9.2
        - DESTINATION="OS=8.2,name=iPhone 5" SDK=iphonesimulator9.2
        - DESTINATION="OS=8.1,name=iPhone 4s" SDK=iphonesimulator9.2
    before_install:
      - gem install cocoapods --no-rdoc --no-ri --no-document --quiet
      - gem install xcpretty --no-rdoc --no-ri --no-document --quiet
      - git submodule update
    script:
      - set -o pipefail
      - xcodebuild -workspace examples/app/Example.xcworkspace -scheme Example -sdk "$SDK" -destination "$DESTINATION" -configuration Debug ONLY_ACTIVE_ARCH=NO build test | xcpretty -c;

Replace "Example" in the `xcodebuild` step with the name of an example app. Add one xcodebuild step
for each example app.

## Bootstrap script

The bootstrap script creates the skeletal directory structure for a GOS repo. This script must be
provided a library name and a path.

Example usage:

    # Create a new directory, ~/workbench/MyLibrary/, populated with the GOS conventional structure.
    ./bootstrap MyLibrary ~/workbench/
