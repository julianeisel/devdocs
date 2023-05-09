# ShaderBuilder

Using the CMAKE option `WITH_GPU_BUILDTIME_SHADER_BUILDER=On` will precompile each shader to
make sure that the syntax is correct and that all the generated code compiles and
links with the main shader code.

Only shaders that are part of the `SHADER_CREATE_INFOS` and `.do_static_compilation(true)`
is set, will be compiled. Enabling this option would reduce compile roundtrips when
developing shaders as during compile time the shaders are validated, compiled and linked
on the used platform.

Shader builder checks against all GPU backends that can run on your system.
