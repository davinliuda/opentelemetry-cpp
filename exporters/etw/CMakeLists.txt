add_library(opentelemetry_exporter_etw INTERFACE)

target_include_directories(
  opentelemetry_exporter_etw
  INTERFACE "$<BUILD_INTERFACE:${CMAKE_CURRENT_LIST_DIR}/include>"
            "$<INSTALL_INTERFACE:include>")

set_target_properties(opentelemetry_exporter_etw PROPERTIES EXPORT_NAME
                                                            etw_exporter)

target_link_libraries(opentelemetry_exporter_etw
                      INTERFACE nlohmann_json::nlohmann_json)

install(
  TARGETS opentelemetry_exporter_etw
  EXPORT "${PROJECT_NAME}-target"
  RUNTIME DESTINATION ${CMAKE_INSTALL_BINDIR}
  LIBRARY DESTINATION ${CMAKE_INSTALL_LIBDIR}
  ARCHIVE DESTINATION ${CMAKE_INSTALL_LIBDIR})

install(
  DIRECTORY include/opentelemetry/exporters/etw
  DESTINATION include/opentelemetry/exporters/
  FILES_MATCHING
  PATTERN "*.h")

if(BUILD_TESTING)
  add_executable(etw_provider_test test/etw_provider_test.cc)
  add_executable(etw_tracer_test test/etw_tracer_test.cc)
  add_executable(etw_logger_test test/etw_logger_test.cc)

  add_executable(etw_perf_test test/etw_perf_test.cc)

  target_link_libraries(etw_provider_test ${GTEST_BOTH_LIBRARIES}
                        opentelemetry_exporter_etw ${CMAKE_THREAD_LIBS_INIT})

  target_link_libraries(etw_tracer_test ${GTEST_BOTH_LIBRARIES}
                        opentelemetry_exporter_etw ${CMAKE_THREAD_LIBS_INIT})

  target_link_libraries(etw_logger_test ${GTEST_BOTH_LIBRARIES}
                        opentelemetry_exporter_etw ${CMAKE_THREAD_LIBS_INIT})

  target_link_libraries(
    etw_perf_test benchmark::benchmark ${GTEST_BOTH_LIBRARIES}
    opentelemetry_exporter_etw ${CMAKE_THREAD_LIBS_INIT})

  gtest_add_tests(
    TARGET etw_provider_test
    TEST_PREFIX exporter.
    TEST_LIST etw_provider_test)
  gtest_add_tests(
    TARGET etw_tracer_test
    TEST_PREFIX exporter.
    TEST_LIST etw_tracer_test)
  gtest_add_tests(
    TARGET etw_logger_test
    TEST_PREFIX exporter.
    TEST_LIST etw_logger_test)

endif() # BUILD_TESTING
