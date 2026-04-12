---
title: 'Zig 0.16.x Reading From File'
date: 2026-04-12T09:57:42+10:00
tags:
  - 'zig'
  - 'io'
---
# Disclaimer

None of the texts and code has been created or proof read by any Generative AI. All of the content on this article has been
manually typed by me.

# Further reading
Rather burying which resources I found the most useful at the bottom, I'd rather provide the them at the top. Here's a
fantastic chapter in [Introduction to Zig - 13 Filesystem and
Input/Output(IO)](https://pedropark99.github.io/zig-book/Chapters/12-file-op.html) that I used to further understand the
new io interface in zig.

# Background
Examples on how to read a file in zig 0.16.x was quite lacking, and scouring through the source file and standard
library implementation meant I had to figure out how all the new interfaces worked. So I wanted to write a short,
pragmatic guide on how the new interfaces coupled. 

# Reading a file
With the new upcoming release of zig 0.16, zig has introduced a new
[`std.Io`](https://ziglang.org/documentation/master/std/#std.Io) interface [(#PR
25592)](https://github.com/ziglang/zig/pull/25592) which looks to deprecate the use of how I know how to read a file in
zig 0.15.2.

Small refresher on how to read a file in zig 0.15.2
```zig
const std = @import("std");

pub fn main() void {
    const file = try std.fs.cwd().openFile("some/file/path.txt", .{});
    defer file.close();
}
```

In zig 0.16 the `std.fs.cwd()` implementation has been removed in favour of using `std.Io`.

Here's how a file reader can be implemented in zig 0.16
```zig
const std = @import("std");

/// Wow! new init interface too?
/// https://ziglang.org/documentation/master/std/#std.process.Init
pub fn main(init: std.process.Init) !void {
    // In order to do I/O operations need an `Io` instance and init provides an io interface
    const io = init.io;

    // `init` also provides some default allocators. We could also use init.gpa
    // const gpa: std.mem.Allocator = init.gpa;
    // This is appropriate for anything that lives as long as the process.
    const arena: std.mem.Allocator = init.arena.allocator();

    try readFile(io, arena, "some.txt");
}

/// Reading a file using a std.Io interface, with a std.mem.Allocator interface
/// This reads the entire contents of the file into memory.
pub fn readFile(io: std.Io, allocator: std.mem.Allocator, filename: []const u8) !void {
    // Use the cwd of the process to read from file
    const cwd = std.Io.Dir.cwd();
    const file = try cwd.openFile(io, filename, .{ .mode = .read_only });
    defer file.close(io);

    const filesize = try file.length(io);
    std.debug.print("file length: {any}\n", .{ filesize });

    // Heap allocated []u8 can be used as a buffer
    const buffer = try allocator.alloc(u8, 4096);
    defer allocator.free(buffer);
    var fr = file.reader(io, buffer);

    // []u8 defined on the stack can also be used, but we'd need to pass a pointer
    // to the buffer and the buffer must also be mutable.
    // var buffer: [4096]u8 = undefined;
    // var fr = file.reader(io, &buffer);

    const fr_buffer = try allocator.alloc(u8, filesize);
    defer allocator.free(fr_buffer);
    fr.interface.readSliceAll(fr_buffer) catch |err| {
        std.log.err("read failed: {}", .{ err });
    };

    std.debug.print("{s}\n", . { fr_buffer });
}
```

If we wanted to test the `readFile()` functionality we can use [`std.testing.io`](https://ziglang.org/documentation/master/std/#std.testing.io)
and [`std.testing.allocator`](https://ziglang.org/documentation/master/std/#std.testing.allocator)
to provide these interfaces.
```zig
test "testing file reading" {
    const io: std.Io = std.testing.io;
    const allocator: std.mem.Allocator = std.testing.allocator;

    try readFile(io, allocator, "some.txt");
}
```
