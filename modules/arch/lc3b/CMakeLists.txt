YASM_RE2C(
    ${PROJECT_SOURCE_DIR}/arch/lc3b/lc3bid.re
    ${PROJECT_BINARY_DIR}/lc3bid.c
    -s
    )

YASM_ADD_MODULE(arch_lc3b
    arch/lc3b/lc3barch.c
    arch/lc3b/lc3bbc.c
    lc3bid.c
    )
