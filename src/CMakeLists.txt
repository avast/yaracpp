##
## CMake build script for the yaracpp library.
##

if(NOT TARGET yaracpp)
	set(SOURCES
		types/yara_match.cpp
		types/yara_meta.cpp
		types/yara_rule.cpp
		yara_detector/yara_detector.cpp
	)

	add_library(yaracpp ${SOURCES})
	target_include_directories(yaracpp PUBLIC "${YARACPP_INCLUDE_DIR}")
	target_link_libraries(yaracpp libyara)
endif()
