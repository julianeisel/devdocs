# Vertex Buffer

## Data conversion

Blender can use a `GPU_COMP_I32`/`GPU_COMP_U32` and use `GPU_FETCH_INT_TO_FLOAT`
to bind it to a float attribute. Vulkan doesn't support this because it adds
no benefit to the GPU.

> NOTE: `GPU_COMP_U8/I8/U16/I16` with `GPU_FETCH_INT_TO_FLOAT` are natively supported
> as they trade in a bit of work to reduce memory bandwidth.

Although we should remove these cases in the code-base, we should still add the data
conversion as add-ons might use them.

``` cpp title="vk_data_conversion.hh"
bool conversion_needed(const GPUVertFormat &vertex_format);
void convert_in_place(void *data, const GPUVertFormat &vertex_format, const uint vertex_len);
```

Based on a `GPUVertFormat` it can be checked if there are some attributes
that requires conversion on the host side.

`convert_in_place` only changes the buffer (`data`) the vertex_format is still the
original.

When binding the vertex buffers to the shader the VkFormat of those
attributes are also changed.

``` cpp title="vk_common.hh"
VkFormat to_vk_format(const GPUVertCompType type,
                      const uint32_t size,
                      const GPUVertFetchMode fetch_mode);
```
`GPU_COMP_I32`/`GPU_COMP_U32` with `GPU_FETCH_INT_TO_FLOAT` would return a
`VK_FORMAT_*_SFLOAT` as the data should already be converted to floats by
calling `convert_in_place`.
