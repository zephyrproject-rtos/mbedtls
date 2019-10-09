if(CONFIG_MBEDTLS)
zephyr_interface_library_named(mbedTLS)

if(CONFIG_MBEDTLS_BUILTIN)
  target_compile_definitions(mbedTLS INTERFACE
	  MBEDTLS_CONFIG_FILE="${CONFIG_MBEDTLS_CFG_FILE}"
	)

  target_include_directories(mbedTLS INTERFACE
	  include
	  configs
	)

  zephyr_library()

  file(GLOB
    mbedtls_sources # This is an output parameter
    library/*.c
    )

  zephyr_library_sources(
    zephyr_init.c
    ${mbedtls_sources}
  )

  zephyr_library_link_libraries(mbedTLS)
else()
  assert(CONFIG_MBEDTLS_LIBRARY "MBEDTLS was enabled, but neither BUILTIN or LIBRARY was selected.")

  # NB: CONFIG_MBEDTLS_LIBRARY is not regression tested and is
  # therefore susceptible to bit rot

  target_include_directories(mbedTLS INTERFACE
	${CONFIG_MBEDTLS_INSTALL_PATH}
	)

  zephyr_link_libraries(
    mbedtls_external
    -L${CONFIG_MBEDTLS_INSTALL_PATH}
    gcc
    )
  # Lib mbedtls_external depends on libgcc (I assume?) so to allow
  # mbedtls_external to link with gcc we need to ensure it is placed
  # after mbedtls_external on the linkers command line.
endif()

endif()
