set(TEST_NAMES
  optimizer_smoke_test
  controller_state_transition_test
  models_test
  noise_generator_test
  parameter_handler_test
  motion_model_tests
  trajectory_visualizer_tests
  utils_test
  path_handler_test
  critic_manager_test
  optimizer_unit_tests
)

foreach(name IN LISTS TEST_NAMES)
  ament_add_gtest(${name}
    ${name}.cpp
  )

  target_link_options(${name} PRIVATE
    "-Wl,--no-as-needed,$<TARGET_FILE:nav2_msgs::nav2_msgs__rosidl_typesupport_cpp>,--as-needed")

  target_link_libraries(${name}
    nav2_msgs::nav2_msgs__rosidl_typesupport_cpp
    mppi_controller
  )

  ament_target_dependencies(${name}
    ${dependencies_pkgs}
  )

  if(${TEST_DEBUG_INFO})
    target_compile_definitions(${name} PUBLIC -DTEST_DEBUG_INFO)
  endif()

endforeach()

# This is a special case requiring linking against the critics library
ament_add_gtest(critics_tests critics_tests.cpp)
target_link_options(critics_tests PRIVATE
  "-Wl,--no-as-needed,$<TARGET_FILE:nav2_msgs::nav2_msgs__rosidl_typesupport_cpp>,--as-needed")
target_link_libraries(critics_tests
  nav2_msgs::nav2_msgs__rosidl_typesupport_cpp
  mppi_controller
  mppi_critics)
ament_target_dependencies(critics_tests ${dependencies_pkgs})
if(${TEST_DEBUG_INFO})
  target_compile_definitions(critics_tests PUBLIC -DTEST_DEBUG_INFO)
endif()
