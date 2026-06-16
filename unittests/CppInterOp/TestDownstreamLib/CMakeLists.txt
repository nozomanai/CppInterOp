add_llvm_library(TestDownstreamLib
  SHARED
  DISABLE_LLVM_LINK_LLVM_DYLIB
  BUILDTREE_ONLY
  TestDownstreamLib.cpp)
add_dependencies(TestDownstreamLib CppInterOpTableGen)
# NDEBUG mirrors cppyy's Release build so the JitCall::Invoke asserts
# don't drag in extra UND refs.
# LLVM's add_llvm_library adds -UNDEBUG in Debug builds, which overrides
# any target_compile_options/target_compile_definitions based approach.
# Remove -UNDEBUG from the target to ensure NDEBUG can be defined.
if(MSVC)
  get_target_property(_opts TestDownstreamLib COMPILE_OPTIONS)
  if(_opts)
    list(FILTER _opts EXCLUDE REGEX "/UNDEBUG|-UNDEBUG")
    set_target_properties(TestDownstreamLib PROPERTIES COMPILE_OPTIONS "${_opts}")
  endif()
  unset(_opts)
endif()
target_compile_definitions(TestDownstreamLib PRIVATE NDEBUG)

set_output_directory(TestDownstreamLib
  BINARY_DIR ${CMAKE_CURRENT_BINARY_DIR}/../bin/$<CONFIG>/
  LIBRARY_DIR ${CMAKE_CURRENT_BINARY_DIR}/../bin/$<CONFIG>/
)

set_target_properties(TestDownstreamLib PROPERTIES FOLDER "Tests")
