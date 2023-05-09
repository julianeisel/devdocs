# Renderdoc

Renderdoc is a widely used open source GPU debugger. Blender has several options
to benefit using renderdoc for debugging.

## Frame capturing

When Blender is compiled with `WITH_RENDERDOC=On` you can start and stop a renderdoc
frame capture from within Blenders' source code.

1. Add `GPU_debug_capture_begin`/`GPU_debug_capture_end` around the code you want to capture.
2. Start renderdoc and launch from within renderdoc using the `--debug-gpu-renderdoc` command
   line parameter
3. Every time the `GPU_debug_capture_begin/end` pair is reached it will automatically record
   a frame capture.

## Command grouping

With the command line parameter `--debug-gpu` blender will add meta-data to the buffers and
commands. This makes it easier to navigate complex frame captures. Command grouping
is automatically enabled when running with the `--debug-gpu-renderdoc` option
