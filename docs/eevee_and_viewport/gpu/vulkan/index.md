# Vulkan backend

The `gpu` module has a generic API that can be used to communicate with
different backends like OpenGL, Metal or Vulkan. This section describes
how the Vulkan backend is structured and gives some background on
specific choices made.

## Vulkan in a nutshell

> NOTE: This is not blender specific and doesn't cover all aspects of vulkan.
> It is used as an introduction how vulkan is structured for people who
> who have some familiarity with OpenGL.
>
> The links in this section navigate to a Blender specific explanation.

Compared to OpenGL index, vertex, uniform & storage buffers, vulkan has only a
single [buffer](buffers.md) type. How the buffer can be used in determined by a
usage bitflag. In short a buffer is a chunk of memory available on the GPU.

There are also [Images](images.md). The memory that an image uses on the GPU cannot be
accessed directly from the host. To change the data of an image, an intermediate buffer
is needed. Reason is that images can be stored more optimal (performance wise) in GPU
memory, but how is device/vendor specific. The intermediate buffer will hide differences
between implementations.

To run code on the GPU [pipelines](pipelines.md) and [shaders](shaders.md) are needed.

> NOTE: the term of a shader doesn't map directly between the definition that
> vulkan uses and the definition that Blender uses.
>
> The term shader in Blender maps to an OpenGL program, which is the combination
> of all different shader stages that are needed in a pipeline.
>
> In Vulkan a shader (module) is compiled GLSL code that can be used as a stage inside
> a pipeline.
>
> From now on we will use shader stage to refer to a single stage and shader to refer
> to the set of shader stages that work together inside a pipeline.

There are 2 main types of pipelines. One for compute tasks and one for graphical tasks. There
are other pipelines as well, but we ignore them for now as Blender doesn't use them (yet).
A pipeline contains everything what needs to happen on the GPU logic-wise during a
single draw or dispatch command. Dispatch commands are used to invoke compute tasks, draw
commands to invoke graphical tasks.

A pipeline has multiple shader stages. A graphics pipeline typically
has a vertex and fragment stage. For each stage a shader module can be assigned.
Although similar to OpenGL, the main difference is that any state change on the GPU
requires a different pipeline. If you need a different blending to store the final
pixel in the framebuffer, you will need another pipeline.

The buffers and images that are needed by a pipeline are organized in descriptor sets. A
descriptor set doesn't contain the buffers and images, it only references existing buffers
and images.

In vulkan a pipeline can have a small number of descriptor sets (typically up to 4).
They are organized based on how likely the references needs to be updated for another
reference. When a reference is changed a new descriptor set needs to be created and
uploaded as the previous one can still be used by another command.

> NOTE: It is possible to swap out a descriptor set for another one. For example when a specific
> combination of resources are often reused. In that case you might not want to recreate a
> descriptor set and upload it to the GPU.

[Push Constants](push_constants.md) is a small buffer (typically 128 or 256 bytes and
device specific) that can be sent with an individual command. Push constants are
typically used for variables in shader stages that are likely to change for each
command. Push constants are faster then using a uniform buffer.

Multiple commands are added to a [command buffer](command_buffer.md) and submitted in one go
to the device command queue. When resources are used by multiple commands inside the command
buffer synchronization needs to happen. It could be that one command is writing to a buffer,
and another command reads it. Vulkan has different ways to influence the synchronization.

> NOTE: It is often said that when you understand the synchronization you will understand how
> and why vulkan is structured in the way it is.

Synchronization can happen between devices and queues and command buffers (semaphores and
fences), between GPU and CPU (fences), between commands and between resource usages inside
the same command buffer/queue (command barriers/memory barriers)

> NOTE: We won't go into to detail how they work as we want to keep this section introductory.
> There are many great vulkan resources that explain in detail why and how these can
> be used.
>
> In blender most synchronization will be hidden for most developers/users, only when
> developing inside the GPU backend these concepts should be understood in more depth.

### Random topics

- [VKFrameBuffer](vk_frame_buffer.md) Blender uses top left as the origin of frame buffers, Vulkan uses bottom left.
- [VKVertexBuffer](vk_vertex_buffer.md) Vulkan only support data conversion when there are
  benefits when using them. Eg saving memory bandwidth vs processing.

## Naming convention

The vulkan backend has some additional naming conventions in order to clarify if a Vulkan native
structure/attribute is passed along or it is from the GPU module. Reasoning is that Vulkan
uses the Prefix `Vk` for their structures and Blender uses `VK` for their structures. To make
the code more readable we added the next naming convention:

- Any parameter, attribute, variable that contains a Vulkan native data type must be prefixed
  with `vk_`. It is not allowed to name parameters, attributes and variables that contains a
  GPU module struct to start with `vk_`.

``` cpp title="Naming example"
// Allowed:
    VKBuffer buffer
    VkBuffer vk_buffer
//Not Allowed:
    VKBuffer vk_buffer
    VkBuffer buffer
```

## Development tools

### Build options

Several build options are available for development.

- `WITH_VULKAN_BACKEND`: Turn this option on to compile blender with Vulkan backend.
- `WITH_VULKAN_GUARDEDALLOC`: Guard driver allocated memory with guardedalloc.

### Validation layers

- Validation Layers

## References

- The vulkan backend is located in `source/blender/gpu/vulkan`.
- Platform specific parts are located in `intern/ghost`. Mainly in `GHOST_ContextVK.cc`.