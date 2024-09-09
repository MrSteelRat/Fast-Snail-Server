add_executable(server main.cc)
target_link_libraries(server PRIVATE
  fss_options
  fss_deps
)
