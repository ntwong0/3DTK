add_executable(test_create_icosphere create_icosphere.cc)
target_link_libraries(test_create_icosphere)

if (WITH_LIBZIP)
  find_package(LibZip)
  if (NOT ${LibZip_FOUND})
    include_directories(${PROJECT_SOURCE_DIR}/3rdparty/libzip-1.0.1/lib/)
    set (LIBZIP_LIBRARY zip)
  endif()
endif()

if (WITH_LIBZIP)
  add_executable(test_zip zip.cc)
  target_link_libraries(test_zip ${LIBZIP_LIBRARY})
endif()

add_custom_target(test_icosphere DEPENDS "scan000.3d" "scan000.frames" "scan000.pose" "scan000.xyz")
add_custom_command(OUTPUT "scan000.3d" "scan000.frames" "scan000.pose" "scan000.xyz" COMMAND test_create_icosphere)

if (WITH_LIBZIP)
  add_custom_target(test_icosphere_zip DEPENDS "normals.zip")
  add_custom_command(OUTPUT "normals.zip" COMMAND test_zip ./ DEPENDS "normals/scan000.3d" "normals/scan000.pose")
endif()

add_custom_target(test_icosphere_normals DEPENDS "normals/scan000.3d" "normals/scan000.pose")
add_custom_command(OUTPUT "normals/scan000.3d" "normals/scan000.pose" COMMAND calc_normals -s 0 -e 0 -f uos -g AKNN -k 20 .)
