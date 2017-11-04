# See README.txt
#
# The main test.cc file also contains tests for the legacy (deprecated) EigehBitIntGemm interface.
# There is no conditional logic in the upstream code for these tests so we follow the README 
# recommendation of simply adding eight

# Add gemmlowp_ prefix as workaround for:
# > The target name "test" is reserved or not valid for certain CMake features

set(eight_bit_int_gemm_src ${GEMMLOWP_ROOT_DIR}/eight_bit_int_gemm/eight_bit_int_gemm.cc)
add_executable(gemmlowp_test test.cc test_data.cc ${eight_bit_int_gemm_src})
add_test(NAME gemmlowp_test COMMAND gemmlowp_test)

set(test_src_files test_allocator.cc test_blocking_counter.cc test_fixedpoint.cc test_math_helpers.cc)
foreach(test_src ${test_src_files})
  get_filename_component(test_name ${test_src} NAME_WE)
  set(test_srcs ${test_src} test_data.cc)
  set(test_name "gemmlowp_${test_name}")
  add_executable(${test_name} ${test_srcs} ${eight_bit_int_gemm_src})
  target_link_libraries(${test_name} gemmlowp)
  add_test(NAME ${test_name} COMMAND ${test_name})
endforeach()
