# ---------------------------------------------------------------------------
# IBSurgeon Full Text Search UDR Library.
#
# The original code was created by Simonov Denis
# for open source project "IBSurgeon Full Text Search UDR".
#
# Copyright (c) 2022 Simonov Denis <sim-mail@list.ru>
# and all contributors signed below.
#
# All Rights Reserved.
# Contributor(s): ______________________________________.
# ----------------------------------------------------------------------------

cmake_minimum_required (VERSION 3.13)

project (luceneudr
    VERSION 1.4.0
    LANGUAGES CXX
)

if(NOT CMAKE_BUILD_TYPE)
    set(CMAKE_BUILD_TYPE Release)
endif()

# WOW64 32-bit?
set(FIREBIRD_DIR "C:/Program Files/Firebird/Firebird_3_0")
set(FIREBIRD_LIB_DIR ${FIREBIRD_DIR}/lib)
set(FIREBIRD_INCLUDE_DIR ${FIREBIRD_DIR}/include)
set(FIREBIRD_UDR_DIR ${FIREBIRD_DIR}/plugins/udr)

add_compile_definitions(WIN32_LEAN_AND_MEAN)

####################################
# library target
####################################
add_library(luceneudr SHARED ${luceneudr_sources})
target_sources(luceneudr PRIVATE
    "src/Analyzers.cpp"
    "src/EnglishAnalyzer.cpp"
    "src/FBFieldInfo.cpp"
    "src/FBUtils.cpp"
    "src/FTS.cpp"
    "src/FTS_HIGHLIGHTER.cpp"
    "src/FTS_MANAGEMENT.cpp"
    "src/FTS_STATISTICS.cpp"
    "src/FTS_TRIGGER_HELPER.cpp"
    "src/FTSHelper.cpp"
    "src/FTSIndex.cpp"
    "src/FTSTrigger.cpp"
    "src/FTSUtils.cpp"
    "src/LuceneAnalyzerFactory.cpp"
    "src/LuceneFiles.cpp"
    "src/LuceneUdr.cpp"
    "src/Relations.cpp"
)

# require C++17 standard
target_compile_features(luceneudr PRIVATE cxx_std_17)
target_compile_definitions(luceneudr PRIVATE HAVE_CONFIG_H)
target_compile_options(luceneudr PRIVATE
#    -Werror

    -Wall
    -Wextra
    -Wpedantic

    -Wno-unused-parameter
)

# find_package(liblucene++ REQUIRED)
# find_package(liblucene++-contrib REQUIRED)
set(LUCENE_DIR "C:/Program Files/lucene++")
set(liblucene++_INCLUDE_DIRS ${LUCENE_DIR}/include)
set(liblucene++_LIBRARY_DIRS ${LUCENE_DIR}/lib)
set(liblucene++_LIBRARIES "lucene++")

set(liblucene++-contrib_INCLUDE_DIRS ${LUCENE_DIR}/include/lucene++)
set(liblucene++-contrib_LIBRARY_DIRS ${LUCENE_DIR}/lib)
set(liblucene++-contrib_LIBRARIES "lucene++-contrib")

include_directories(${liblucene++_INCLUDE_DIRS})
include_directories(${liblucene++-contrib_INCLUDE_DIRS})
include_directories(${FIREBIRD_INCLUDE_DIR})
include_directories(C:/local/boost_1_83_0)

link_directories(${liblucene++_LIBRARY_DIRS})
link_directories(${liblucene++-contrib_LIBRARY_DIRS})
link_directories(${FIREBIRD_LIB_DIR})

####################################
# include directories
####################################
target_include_directories(luceneudr PRIVATE
    ${liblucene++_INCLUDE_DIRS}
    ${liblucene++-contrib_INCLUDE_DIRS}
    ${FIREBIRD_INCLUDE_DIR}
    "include"
)

####################################
# link directories
####################################
target_link_directories(luceneudr PRIVATE
    ${liblucene++_LIBRARY_DIRS}
    ${liblucene++-contrib_LIBRARY_DIRS}
)

target_link_libraries(luceneudr
    -lstdc++fs
    ${liblucene++_LIBRARIES}
    ${liblucene++-contrib_LIBRARIES}
)

# target_include_directories(luceneudr PRIVATE "include")
# target_include_directories(luceneudr PRIVATE ${FIREBIRD_INCLUDE_DIR})
# target_include_directories(luceneudr PRIVATE "C:/local/boost_1_83_0")

# target_link_directories(luceneudr PRIVATE ${FIREBIRD_LIB_DIR})
# target_link_directories(luceneudr PRIVATE "C:/local/boost_1_83_0/lib64-msvc-14.2")
# target_link_directories(luceneudr PRIVATE ${liblucene++_LIBRARY_DIRS})
# target_link_directories(luceneudr PRIVATE ${liblucene++-contrib_LIBRARY_DIRS})

install(TARGETS luceneudr DESTINATION ${FIREBIRD_UDR_DIR})
install(FILES "sql/fts$install.sql"
              "sql/fts$install_1.sql"
              "sql/fts$uninstall.sql"
              DESTINATION ${FIREBIRD_UDR_DIR}
)

