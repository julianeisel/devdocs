# GPU Module

The GPU module is an abstraction layer between Blender and an Operating System
Graphics Library layer (GL). These GLs are abstracted away in GPUBackends. There
is a GLBackend that provides support to OpenGL 3.3 on Windows, Mac and Linux.
There is also a Metal backend for Apple devices. Vulkan backend is currently
in development.

GPU module can be used to draw geometry or perform computational tasks using a
GPU. This overview is targeted to developers who want to have a quick start how
they can use the GPU module to draw or compute. Basic knowledge of an GL (OpenGL
core profile 3.3 or similar) is required as we use similar concepts.

## Drawing pipeline

This section gives an overview of the drawing pipeline of the GPU module.

``` mermaid
classDiagram
direction LR

class GPUBatch
class GPUShader {
    -GLSL vertex_code
    -GLSL fragment_code
}
class GPUFramebuffer
class GPUVertBuf
class GPUIndexBuffer
class GPUPrimType {
    <<Enumeration>>
    GPU_PRIM_POINTS,
    GPU_PRIM_LINES,
    GPU_PRIM_TRIS,
    ...
}
class GPUShaderInterface
class GPUTexture

GPUBatch o--> GPUIndexBuffer
GPUBatch o--> GPUVertBuf
GPUBatch *--> GPUPrimType
GPUBatch o..> GPUShader: draws using
GPUShader o..> GPUFramebuffer: onto
GPUShader *--> GPUShaderInterface
GPUFramebuffer o--> GPUTexture


```

## Textures

Textures are used to hold pixel data. Textures can be 1, 2 or 3 dimensional,
cubemap and an array of 2d textures/cubemaps. The internal storage of a texture
(how the pixels are stored in memory on the GPU) can be set when creating a
texture.

``` cpp title="Create a texture"
/* Create an empty texture with HD resolution where pixels are stored as half floats. */
GPUTexture *texture = GPU_texture_create_2d("MyTexture", 1920, 1080, 1, 0, GPU_RGBA16F, NULL);
```

## Frame buffer

A frame buffer is a group of textures you can render onto. These textures are
arranged in a fixed set of slots. The first slot is reserved for a depth/stencil
buffer. The other slots can be filled with regular textures, cube maps, or layer
textures.

GPU_framebuffer_ensure_config is used to create/update the configuration of a
framebuffer.

``` cpp title="Create a framebuffer"
GPUFramebuffer *fb = NULL;
GPU_framebuffer_ensure_config(&fb, {
  GPU_ATTACHMENT_NONE, // Slot reserved for depth/stencil buffer.
  GPU_ATTACHMENT_TEXTURE(texture),
})
```

## Shader Create Info

The GPU module supports multiple GL backends. The challenge of multiple GL
backends is that GLSL is different on all those platforms. It isn't possible
to compile an OpenGL GLSL on Vulkan as the GLSL differs. The big differences
between the GLSL's are how they define and locate resources.

This section of the documentation will handle how to create GLSL that can
safely be cross compiled to different backends.

### GPUShaderCreateInfo

#### Defining a new compile unit

When creating a new compile unit to contain a GPUShaderCreateInfo definition
it needs to be added to the `SHADER_CREATE_INFOS` in `gpu/CMakeLists.txt` this
will automatically register the definition in a registry.

Each of the compile unit should include `gpu_shader_create_info.h`.

#### Interface Info

Interfaces are data that are passed between shader stages (Vertex => Fragment stage).
Attributes can be `flat`, `smooth` or `no_perspective` describing the interpolation
mode between.

``` cpp title="Example interface info"
GPU_SHADER_INTERFACE_INFO(text_iface, "")
    .flat(Type::VEC4, "color_flat")
    .no_perspective(Type::VEC2, "texCoord_interp")
    .flat(Type::INT, "glyph_offset")
    .flat(Type::IVEC2, "glyph_dim")
    .flat(Type::INT, "interp_size")
```

#### Shader Info

Shader Info describes

- Where to find required data (vertex_in, push constant).
- Textures/samplers to use (sampler)
- Where to store the final data (fragment_out)
- It describes the data format between the shader stages (vertex_out).
- The source code of each stage (vertex_source, fragment_source)

Shader infos can reuse other infos to reduce code duplication (`additional_info` would
load the data from the given shader info into the new shader info.

The active GPU backend will adapt the GLSL source to generate those part of the code.

``` cpp title="Example Shader Info"
GPU_SHADER_CREATE_INFO(gpu_shader_text)
    // vertex_in define vertex buffer inputs. They will be passed to the vertex stage.
    .vertex_in(0, Type::VEC4, "pos")
    .vertex_in(1, Type::VEC4, "col")
    .vertex_in(2, Type::IVEC2, "glyph_size")
    .vertex_in(3, Type::INT, "offset")

    // vertex_out define the structure of the output of the vertex stage.
    // This would be the input of the fragment stage or geometry stage.
    .vertex_out(text_iface)

    // definition of the output of the fragment stage.
    .fragment_out(0, Type::VEC4, "fragColor")

    // Flat uniforms aren't supported anymore and should be added as push constants.
    // Note that push constants are limited to 128 bytes. Use uniform buffers
    // when more space is required.
    // Internal Matrices are automatically bound to push constants when they exists.
    // Matrices inside a uniform buffer is the responsibility of the developer.
    .push_constant(0, Type::MAT4, "ModelViewProjectionMatrix")

    // Define a sampler location.
    .sampler(0, ImageType::FLOAT_2D, "glyph", Frequency::PASS)

    // Specify the vertex and fragment shader source.
    // dependencies can be automatically included by using `#pragma BLENDER_REQUIRE`
    .vertex_source("gpu_shader_text_vert.glsl")
    .fragment_source("gpu_shader_text_frag.glsl")

    // Add all info of the GPUShaderCreateInfo with the given name.
    // Provides limited form of inheritance.
    .additional_info("gpu_srgb_to_framebuffer_space")

    // Create info is marked to be should be compilable.
    // By default a create info is not compilable.
    // Compilable shaders are compiled when using shader builder.
    .do_static_compilation(true);
```

##### Shader Source Order

Shader source order does not follow the order of the methods call made to the
create info. Instead it follows this fixed order:

- Standard Defines: GPU module defines (`GPU_SHADER`, `GPU_VERTEX_SHADER`, OS, GPU vendor, and extensions) and MSL glue.
- Create Info Defines: `.define`.
- Typedef Sources: `.typedef_source`.
- Resources Declarations: `.sampler`, `.image`, `.uniform_buf` and `.storage_buf`.
- Layout Declarations: `.geometry_layout`, `.local_group_size`.
- Interface Declarations: `.vertex_in`, `.vertex_out`, `.fragment_out`, `.fragment_out`.
- Main Dependencies: All files inside `#pragma BLENDER_REQUIRE` directives.
- Main: Shader stage source file `.vertex_source`, `.fragment_source`, `.geometry_source` or `.compute_source`.
- NodeTree Dependencies: All files needed by the nodetree functions. Only for shaders from Blender Materials.
- NodeTree: Definition of the nodetree functions (ex: `nodetree_surface()`). Only for shaders from Blender Materials.

#### Buffer Structures

Previously structs that were used on CPU/GPU would be written twice. Once using
the CPU data types and once that uses the GPU data types. Developers were
responsible to keep those structures consistent.

Shared structs can be defined in 'shader_shared' header files. For example the
`GPU_shader_shared.h`. These headers can be included in C and CPP compile units.

``` cpp
/* In GPU_shader_shared.h */
struct MyStruct {
  float4x4 modelMatrix;
  float4 colors[3];
  bool1 do_fill;
  float dim_factor;
  float thickness;
  float _pad;
};
BLI_STATIC_ASSERT_ALIGN(MyStruct, 16)
```

Developer is still responsible to layout the struct (alignment and padding) so
it can be used on the GPU.

> NOTE: See [[Style_Guide/GLSL#Shared_Shader_Files|these rules]] about correctly
> packing members.

``` cpp
GPU_SHADER_CREATE_INFO(my_shader)
  .typedef_source("GPU_shader_shared.h")
  .uniform_buf(0, "MyStruct", "my_struct");
```

This will create a uniform block binding at location 0 with content of type
`MyStruct` named `my_struct`. The struct members can then be accessed also using
`my_struct` (ex: `my_struct.thickness`).

Uniform and storage buffers can also be declared as array of struct like this:

``` cpp
GPU_SHADER_CREATE_INFO(my_shader)
  .typedef_source("GPU_shader_shared.h")
  .storage_buf(0, "MyStruct", "my_struct[]");
```

A shader create info can contain multiple 'typedef_source'. They are included
only once and before any resource declaration (see gpu shader source ordering).

#### Geometry shaders

Due to specific requirements of certain gpu backend input and output parameters of this stage
should always use a named structure.

#### Compute shaders

For compute shaders the workgroup size must be defined. This can be done by calling
the `local_group_size` function. This function accepts 1-3 parameters to define the
local working group size for the x, y and z dimension.

``` cpp
GPU_SHADER_CREATE_INFO(draw_hair_refine_compute)
    /* ... */
    /* define a local group size where x=1 and y=1, z isn't defined. Missing parameters would fallback
     * to the platform default value. For OpenGL 4.3 this is also 1. */
    .local_group_size(1, 1)
    .compute_source("common_hair_refine_comp.glsl")
    /* ... */
```

### C & C++ Code sharing

Code that needs to be shared between CPU and GPU implementation can also be put into
'shader_shared' header files. However, only a subset of C and C++ syntax is allowed
for cross compilation to work:

- No arrays except as input parameters.
- No parameter reference `&` and likewise `out` and `inout`.
- No pointers or references.
- No iterators.
- No namespace.
- No template.
- Use float suffix by default for float literals to avoid double promotion in C++.
- Functions working on floats (ex: `round()`, `exp()`, `pow()` ...) might have different
  implementation on CPU and GPU.
> NOTE: See {{BugReport|103026}} for more detail.

You can also declare `enum` inside these files. They will be correctly translated by
our translation layer for GLSL compilation. However some rules to apply:

- Always use `u` suffix for enum values. GLSL do not support implicit cast and enums are treated as `uint` under the hood.
- Define all enum values. This is to simplify our custom pre-processor code.
- (C++ only) Always use `uint32_t` as underlying type (`enum eMyEnum : uint32_t`).
- (C only) do '''NOT''' use enum types inside UBO/SSBO structs and use `uint` instead (because `enum` size is implementation dependent in C).

See [Shader builder](shader_builder.md) for a validation tool for shaders.

## Shader

A GPUShader is a program that runs on the GPU. The program can have several stages
depending on the its usage. When rendering geometry it should at least have a vertex and fragment stage, it can have an optional geometry stage. It is not recommended to use geometry stages as Apple doesn't have support for it.

``` cpp title="Create shader"
GPUShader *sh_depth = GPU_shader_create_from_info_name("my_shader");
```

This will lookup the shader create info with the name `my_shader`, loads and compile
the vertex and fragment stage and link the stages into a program that can be used on
the GPU. It also generates a GPUShaderInterface that handles lookup to input parameters
(attributes, uniforms, uniform buffers, textures and shader storage buffer objects).

### Geometry

Geometry is defined by a `GPUPrimType`, one index buffer (IBO) and one or more vertex
buffers (VBOs). The GPUPrimType defines how the index buffer should be interpreted.

Indices inside the index buffer define the order how to read elements from the vertex
buffer(s). Vertex buffers are a table where each row contains the data of an element.
When multiple vertex buffers are used they are considered to be different columns of
the same table. This matches how GL backends organize geometry on GPUs.

Index buffers can be created by using a `GPUIndexBufferBuilder`

``` cpp title="Create Index Buffer"
GPUIndexBufBuilder ibuf
/* Construct a builder to create an index buffer that has 6 indexes.
 * And the number of elements in the vertex buffer is 12. */
GPU_indexbuf_init(&ibuf GPU_PRIM_TRIS, 6, 12);

GPU_indexbuf_add_tri_verts(&ibuf, 0, 1, 2);
GPU_indexbuf_add_tri_verts(&ibuf, 2, 1, 3);
GPU_indexbuf_add_tri_verts(&ibuf, 4, 5, 6);
GPU_indexbuf_add_tri_verts(&ibuf, 6, 5, 7);
GPU_indexbuf_add_tri_verts(&ibuf, 8, 9, 10);
GPU_indexbuf_add_tri_verts(&ibuf, 10, 9, 11);

GPUIndexBuf *ibo = GPU_indexbuf_build(&builder)
```

Vertex buffers contain data and attributes inside vertex buffers should match the attributes of the shader.
Before a buffer can be created, the format of the buffer should be defined.

``` cpp title="Create Vertex Format"
static GPUVertFormat format = {0};
GPU_vertformat_clear(&format);
GPU_vertformat_attr_add(&format, "pos", GPU_COMP_32, 2, GPU_FETCH_FLOAT);
```

``` cpp title="Create Vertex Buffer"
GPUVertBuf *vbo = GPU_vertbuf_create_with_format(&format);
GPU_vertbuf_data_alloc(vbo, 12);
```

``` cpp title="Fill vertex buffer with data"
for (int i = 0; i < 12; i ++) {
 GPU_vertbuf_attr_set(vbo, pos.id, i, positions[i]);
}
```

### Batch

Use GPUBatches to draw geometry. A GPUBatch combines the geometry with a shader
and its parameters and has functions to perform a draw call. To perform a draw
call the next steps should be taken.

1. Construct its geometry.
2. Construct a GPUBatch with the geometry.
3. Attach a GPUShader to the GPUBatch with the `GPU_batch_set_shader` function or attach a built in shader using the `GPU_batch_program*` functions.
4. Set the parameters of the GPUShader using the `GPU_batch_uniform*`/`GPU_batch_texture_bind` functions.
5. Perform a `GPU_batch_draw*` function.

This will draw on the geometry on the active frame buffer using the shader and the loaded parameters.

> NOTE: GPUTextures can be used as render target or as input of a shader, but not inside the same drawing call.

### Immediate mode and built in shaders

To ease development for drawing panels/UI buttons the GPU module provides an
immediate mode. This is a wrapper on top of what is explained above, but in a
more legacy opengl fashion.

Blender provides builtin shaders. This is widely used to draw the user interface.
A shader can be activated by calling `immBindBuiltinProgram`

``` cpp
immBindBuiltinProgram(GPU_SHADER_2D_UNIFORM_COLOR);
```

This shader program needs a vertex buffer with a pos attribute, and a color can be set as uniform.

``` cpp
GPUVertFormat *format = immVertexFormat();
uint pos = GPU_vertformat_attr_add(format, "pos", GPU_COMP_F32, 2, GPU_FETCH_FLOAT);
```

``` cpp
/* Set the color attribute of the shader. */
immUniformColor4f(0.0f, 0.5f, 0.0f, 1.0f);
```

Fill the vertex buffer with the starting and ending position of the line to draw.
``` cpp
/* Construct a line index buffer with 2 elements (start point and end point to draw) */
immBegin(GPU_PRIM_LINES, 2);
immVertex2f(pos, 0.0, 100.0);
immVertex2f(pos, 100.0, 0.0);
immEnd();
```

By calling `immEnd` the data drawn on the GPU.

> NOTE: Use GPUBatches directly in cases where performance matters. Immediate mode buffers aren't cached, which can lead to poor performance.

## Tools

- Validate shaders when compiling Blender using [Shader builder](shader_builder.md)
- GPU debugging [Renderdoc](renderdoc.md)