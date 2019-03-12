##
## CMake build script for libyara.
##

if(NOT TARGET yara)
	include(ProcessorCount)
	include(ExternalProject)

	find_package(Threads REQUIRED)

	ProcessorCount(CPUS)

	set(YARA_DIR          ${CMAKE_CURRENT_BINARY_DIR}/yara/src/yara)
	set(YARA_INCLUDE_DIR  ${YARA_DIR}/libyara/include)
	set(YARA_LIBRARY_NAME "libyara")

	if(MSVC)
		if(CMAKE_SIZEOF_VOID_P EQUAL 8)
			set(PLATFORM "x64")
			set(YARA_LIBRARY_NAME "${YARA_LIBRARY_NAME}64")
		else()
			set(PLATFORM "x86")
			set(YARA_LIBRARY_NAME "${YARA_LIBRARY_NAME}32")
		endif()

		set(YARA_WIN_DIR       ${YARA_DIR}/windows/vs2015)
		set(YARA_SOLUTION      ${YARA_WIN_DIR}/yara.sln)
		set(YARA_LIBRARY_DIR   ${YARA_WIN_DIR}/libyara/$<CONFIGURATION>)
		set(YARA_BUILD_COMMAND "${CMAKE_VS_MSBUILD_COMMAND}"
			/m:${CPUS}
			/p:Platform=${PLATFORM}
			/p:Configuration=$<CONFIGURATION>
			/p:PlatformToolset=${CMAKE_VS_PLATFORM_TOOLSET}
			/p:WindowsTargetPlatformVersion=${CMAKE_VS_WINDOWS_TARGET_PLATFORM_VERSION}
			${YARA_SOLUTION})
	else()
		set(YARA_LIBRARY_DIR   ${YARA_DIR}/libyara/.libs)
		set(YARA_BUILD_COMMAND make -j${CPUS})
	endif()

	ExternalProject_Add(yara
		PREFIX "yara"
		URL https://github.com/avast-tl/yara/archive/323fe8c5d6b07b519c4fa898dc1a39106feacb78.zip
		URL_HASH SHA256=d5ad14a99c5492edb1c144fee3eeb5bd1de8d66b114631d76cdb6d05456a7969
		DOWNLOAD_NAME yara.zip
		BUILD_IN_SOURCE 1
		CONFIGURE_COMMAND ""
		BUILD_COMMAND ""
			${YARA_BUILD_COMMAND}
		INSTALL_COMMAND ""
	)

	if(NOT MSVC)
		ExternalProject_Add_Step(yara configure-linux
			WORKING_DIRECTORY ${YARA_DIR}
			DEPENDEES configure
			DEPENDERS build
			COMMAND ./bootstrap.sh
			COMMAND ./configure
				--prefix=${YARA_INSTALL_DIR}
				--enable-macho
				--disable-shared
				--without-crypto
				CC=${CMAKE_C_COMPILER}
		)
	endif()

	set(LIBRARIES
		${YARA_LIBRARY_DIR}/${YARA_LIBRARY_NAME}${CMAKE_STATIC_LIBRARY_SUFFIX}
		${CMAKE_THREAD_LIBS_INIT}
	)

	add_library(libyara INTERFACE)
	set_target_properties(libyara PROPERTIES INTERFACE_LINK_LIBRARIES "${LIBRARIES}")
	target_include_directories(libyara INTERFACE ${YARA_INCLUDE_DIR})
	add_dependencies(libyara yara)
endif()
