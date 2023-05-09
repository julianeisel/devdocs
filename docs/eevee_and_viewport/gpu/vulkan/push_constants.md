
# Push constants

Push constants is a way to quickly provide a small amount of uniform data to shaders.
It should be much quicker than UBOs but a huge limitation is the size of data - spec
requires 128 bytes to be available for a push constant range. There are platforms
that provide more data (Mesa+RDNA provides 256 bytes).

In Blender some shader requires more data than available as push constant. As shaders
can also be part of an add-on we don't have full control on the data size.

> NOTE: As of February 2023 there are 50 shaders in blender that are between 128 and 256 bytes.
> Most of them are related to point clouds drawing. There are also 4 shaders larger than
> 256 bytes. They are for widget drawing and Eevee motion blur.
>
> - Widget drawing must be migrated to use uniform buffers.
> - The Eevee motion blur shaders are part of Eevee-legacy and will be replaced
>   with Eevee-next, that doesn't have this issue.


## Storage types

Blender should be able to work even when shaders use more push constants than can fit
inside the limits of the physical device. Therefore we provide 2 storage types for
storing/communicating push constants with the shader.

- `StorageType::PUSH_CONSTANTS`: will be selected when the push constants fits inside the limits
  of the physical device.
- `StorageType::UNIFORM_BUFFER`: will be selected when the push constants don't fit inside the
  limits of the physical device.

Uniform buffers can handle upto 64kb of data, but require require more memory to store the
same data as it requires std140 memory layout. See below for more information about the
different memory layouts.

The selection which storage type will be used in determined in the shader interface
`VKShaderInterface`.

``` mermaid
classDiagram

class StorageType{
  <<Enumeration>>
    NONE
    PUSH_CONSTANTS
    UNIFORM_BUFFER
}

```

## Memory layout

Push constants memory layout is std430. Uniform buffers is std140.
`vk_memory_layout.hh/cc` provides some utilities to modify memory based on the needed memory
layout. A small overview of differences between std140 and std430:

- In std140 each element inside an array (`float[]`) are aligned at 16 bytes; in std430 they
  are aligned based on the alignment the element type. In this case `float` that are aligned at
  4 bytes.


