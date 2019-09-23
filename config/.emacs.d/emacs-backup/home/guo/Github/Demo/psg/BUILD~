cc_binary(
    name = "demo",
    srcs = ["demo.cc"],
    deps = [":lib3"],
)

cc_library(
    name = "lib3",
    hdrs = ["lib3.h"],
    deps = [":lib2", "//boost:boost"],
)

cc_library(
    name = "lib2",
    hdrs = ["lib2.h"],
    deps = [":lib1", "//boost:boost"],
)

cc_library(
    name = "lib1",
    hdrs = ["lib1.h"],
    deps = ["//boost:boost"],
)
