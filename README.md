# protobuf.zig

> Forked from https://github.com/Arwalk/zig-protobuf

-------

A Google Protocol Buffers implementation in Zig. Primarily targets
[proto3](https://protobuf.dev/programming-guides/proto3/).

## Installation

This package requires only the latest stable version of Zig, 0.15.1.

It can be added as a dependency to your project through the package manager.

1. Add `protobuf` to your `build.zig.zon`.
    ```sh
    zig fetch --save "git+https://github.com/mochalins/protobuf.zig#master"
    ```
2. Use the `protobuf` module. In your `build.zig`'s build function, add the
   dependency to your target module.

```zig
pub fn build(b: *std.Build) !void {
    // first create a build for the dependency
    const protobuf_dep = b.dependency("protobuf", .{
        .target = target,
        .optimize = optimize,
    });

    // ...

    // and lastly use the dependency as a module
    exe.root_module.addImport("protobuf", protobuf_dep.module("protobuf"));
}
```

## Usage

Once `protobuf.zig` is added as a dependency into your project, it can be used
to generate Zig implementation files from the project's `.proto` files through
the build system.

```zig
// build.zig

const protobuf = @import("protobuf");

pub fn build(b: *std.Build) !void {
    // first create a build for the dependency
    const protobuf_dep = b.dependency("protobuf", .{
        .target = target,
        .optimize = optimize,
    });

    // ...

    const gen_proto = b.step("gen-proto", "generates zig files from protocol buffer definitions");

    const protoc_step = protobuf.RunProtocStep.create(b, protobuf_dep.builder, target, .{
        // out directory for the generated zig files
        .destination_directory = b.path("src/proto"),
        .source_files = &.{
            "protocol/all.proto",
        },
        .include_directories = &.{},
    });

    gen_proto.dependOn(&protoc_step.step);
}
```

When the `gen-proto` step is added to your project's build steps, it may be
used by invoking `zig build gen-proto` from your project's root directory.
