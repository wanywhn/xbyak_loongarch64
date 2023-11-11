set(TARGET add add2 bf bf_loongarch cpuinfo)
foreach(src IN ITEMS ${TARGET})
    add_executable(${src} ${src}.cpp)
    target_link_libraries(${src} PRIVATE xbyak_loongarch64)
    set_target_properties(${src} PROPERTIES
            CXX_STANDARD 11
            CXX_STANDARD_REQUIRED YES
            CXX_EXTENSIONS NO)
endforeach()