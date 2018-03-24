# Examples

## Basics

### [01 - Triangle](examples/triangle/)

Most basic example. Renders a colored triangle using an indexed vertex buffer. Vertex and index data are uploaded to device local memory using so-called "staging buffers". Uses a single pipeline with basic shaders loaded from SPIR-V and and single uniform block for passing matrices that is updated on changing the view. This example is far more explicit than the other examples and is meant to be a starting point for learning Vulkan from the ground up. Much of the code is boilerplate that you'd usually encapsulate in helper functions and classes (which is what the other examples do).

### [02 - Pipelines](examples/pipelines/)

[Pipeline state objects](https://www.khronos.org/registry/vulkan/specs/1.0/xhtml/vkspec.html#pipelines) replace the biggest part of the dynamic state machine from OpenGL, baking state information for culling, blending, rasterization, etc. and shaders into a fixed state that can be optimized much easier by the implementation. This example uses three different PSOs for rendering the same scene with different visuals and shaders and also demonstrates the use of [pipeline derivatives](https://www.khronos.org/registry/vulkan/specs/1.0/xhtml/vkspec.html#pipelines-pipeline-derivatives).

### [03 - Dynamic uniform buffers](examples/dynamicuniformbuffer/)

Demonstrates the use of dynamic uniform buffers for rendering multiple objects with different matrices from one big uniform buffer object. Sets up one big uniform buffer that contains multiple model matrices that are dynamically addressed upon descriptor binding time. This minimizes the number of descriptor sets required and may help in optimizing memory writes by e.g. only doing partial updates to that memory.

### [04 - Push constants](examples/pushconstants/)

Demonstrates the use of push constants for updating small blocks of shader data at command buffer creation time, without having to use a uniform buffer. Displays several light sources with position updates through a push constant block in a separate command buffer.

### [05 - Specialization constants](examples/specializationconstants/)

Demonstrates the use of SPIR-V specialization constants used to specify shader constants at pipeline creation time. The example uses one "uber" shader with different lighting paths (phong, toon, texture mapped) from which all pipelines are build, with a specialization constant used to select the shader path to be used for that pipeline at creation time.

### [06 - Texture mapping](examples/texture/)
Shows how to upload a 2D texture into video memory for sampling in a shader. Loads a compressed texture into a host visible staging buffer and copies all mip levels to a device local optimal tiled image for best performance.

Also demonstrates the use of combined image samplers. Samplers are detached from the actual texture image and only contain information on how an image is sampled in the shader.

### [07 - Cube map textures](examples/texturecubemap/)

Building on the basic texture loading example, a cubemap texture is loaded into a staging buffer and is copied over to a device local optimal image using buffer to image copies for all of it's faces and mip maps.

The demo then uses two different pipelines (and shader sets) to display the cubemap as a skybox (background) and as a source for reflections.

### [08 - Texture arrays](examples/texturearray/)

Texture arrays allow storing of multiple images in different layers without any interpolation between the layers.
This example demonstrates the use of a 2D texture array with instanced rendering. Each instance samples from a different layer of the texture array.

### [09 - Mesh rendering](examples/mesh/)
Uses [assimp](https://github.com/assimp/assimp) to load a mesh from a common 3D format including a color map. The mesh data is then converted to a fixed vertex layout matching the shader vertex attribute bindings.

### [10 - Offscreen rendering](examples/offscreen/)
Shows how to do basic offscreen rendering. Uses a separate framebuffer with color and depth attachments (that is not part of the swap chain) to render the mirrored scene off screen in the first pass. The second pass then samples from the color attachment of that framebuffer for rendering a mirror surface.

### [11 - CPU particle system](examples/particlefire/)
CPU based point sprite particle system simulating a fire. Particles and their attributes are stored in a host visible vertex buffer that's updated on the CPU on each frame. Demonstrates how to update vertex buffer per frame. Also makes use of pre-multiplied alpha for rendering particles with different blending modes (smoke and fire) in one single pass.

### [xx - texture3d]()

```missing description```

### [xx - stencilbuffer]()
```missing description```

### [xx - subpasses]()
```missing description```

## Advanced

### [01 - Scene rendering](examples/scenerendering/)
This example demonstrates a way to render a complex scene consisting of multiple meshes with different materials and textures. It makes use of separate per-material descriptor sets for passing texturing information and uses push constants to pass material properties to the shaders upon pipeline creation. Also shows how to use multiple descriptor sets simultaneously with the new GLSL "set" layout qualifier introduced with [GL_KHR_vulkan_glsl](https://www.khronos.org/registry/vulkan/specs/misc/GL_KHR_vulkan_glsl.txt).

### [02 - Multi sampling](examples/multisampling/)
Demonstrates the use of resolve attachments for doing multisampling. Instead of doing an explicit resolve from a multisampled image this example creates multisampled attachments for the color and depth buffer and sets up the render pass to use these as resolve attachments that will get resolved to the visible frame buffer at the end of this render pass. To highlight MSAA the example renders a mesh with fine details against a bright background. Here is a [screenshot without MSAA](./screenshots/multisampling_nomsaa.png) to compare.

### [03 - High dynamic range](examples/hdr/)
Demonstrates high dynamic range rendering using floating point texture and framebuffer formats, extending the internal image precision range from the usual 8 Bits used in LDR to 16/32 bits. Also adds HDR bloom on top of the scene using a separable blur filter and manual exposure via tone mapping.

### [04 - Shadow mapping](examples/shadowmapping/)
Dynamic shadows from a ```directional light source``` in two passes. The first pass renders the scene depth from the light's point-of-view into a separate framebuffer attachment with a different (higher) resolution. The second pass renders the scene from the camera's point-of-view and compares the depth value of the texels with the one stored in the offscreen depth attachment (which the shader directly samples from) to determine whether a texel is shadowed or not and then applies a PCF filter to smooth out shadow borders. To avoid shadow artefacts the dynamic depth bias state ([vkCmdSetDepthBias](https://www.khronos.org/registry/vulkan/specs/1.0/man/html/vkCmdSetDepthBias.html)) is used to apply a constant and slope depth bias factor.

### [05 - Cascaded shadow mapping](examples/shadowmappingcascade/)
Implements a technique that splits up the camera frustum into multiple frustums, with each getting it's own full-res shadow map (stored in a layered texture). In the final scene rendering pass the shader then selects the proper depth map layer (cascade) by comparing the fragment's depth against cascade split depths. This results in a better shadow map resolution distribution with objects closer to the camera getting the highest shadow map resolution. This is esp. useful in large open scenes with high shadow draw distances where a single depth map would result in a poor shadow map resolution coverage.

### [06 - Omnidirectional shadow mapping](examples/shadowmappingomni/)
Dynamic shadows from a ```point light source```. Uses a dynamic 32 bit floating point cube map for a point light source that casts shadows in all directions (unlike projective shadow mapping). The cube map faces contain the distances from the light sources, which are then used in the final scene rendering pass to determine if the fragment is shadowed or not.

### [07 - Run-time mip-map generation](examples/texturemipmapgen/)
Generates a complete mip-chain at runtime (instead of using mip levels stored in texture file) by blitting from one mip level down to the next smaller size until the lower end of the mip chain (1x1 pixels is reached). This is done using image blits and proper image memory barriers.   

### [08 - Skeletal animation](examples/skeletalanimation/)
This example loads and displays a rigged COLLADA model including animations. Bone weights are extracted for each vertex and are passed to the vertex shader together with the final bone transformation matrices for vertex position calculations.

### [09 - Capturing screenshots](examples/screenshot/)
```misssing description```

## Compute shader

### [01 - Image processing](examples/computeshader/)
Demonstrates the basic use of a separate compute queue (and command buffer) to apply different convolution kernels on an input image in realtime.

### [02 - GPU particle system](examples/computeparticles/)
Attraction based particle system. A shader storage buffer is used to store particle on which the compute shader does some physics calculations. The buffer is then used by the graphics pipeline for rendering with a gradient texture for. Demonstrates the use of memory barriers for synchronizing vertex buffer access between a compute and graphics pipeline

### [03 - N-body simulation](examples/computenbody/)
Implements a N-body simulation based particle system with multiple attractors and particle-to-particle interaction using two passes separating particle movement calculation and final integration. Also shows how to use ```shared compute shader memory``` for a significant performance boost.

### [04 - Ray tracing](examples/raytracing/)
Implements a simple ray tracer using a compute shader. No primitives are rendered by the traditional pipeline except for a fullscreen quad that displays the ray traced results of the scene rendered by the compute shaders. Also implements shadows and basic reflections.

### [05 - Cloth simulation](examples/computecloth/)
```missing description```

### [06 - Cull and LOD](examples/computecullandlod/)
Based on ```indirect drawing``` this example uses a compute shader for visibility testing using ```frustum culling``` and ```level-of-detail selection``` based on object's distance to the viewer. A compute shader is applied to the indirect draw commands buffer that updates the indirect draw calls depending on object visibility and camera distance. This moves all visibility calculations to the GPU so the indirect draw buffer can stay in device local memory without having to map it back to the host for CPU-based updates.

## Performance (?)

### [01 - Multi threaded command buffer generation](examples/multithreading/)
This example demonstrates multi threaded command buffer generation. All available hardware threads are used to generated n secondary command buffers concurrent, with each thread also checking object visibility against the current viewing frustum. Command buffers are rebuilt on each frame. Once all threads have finished (and all secondary command buffers have been constructed), the secondary command buffers are executed inside the primary command buffer and submitted to the queue.

### [02 - Instancing](examples/instancing/)
Uses instancing for rendering multiple instances of the same mesh using different attributes. A secondary vertex buffer containing instanced data (in device local memory) is used to pass instanced data to the shader via vertex attributes, including a texture layer index for using different textures per-instance. Also shows how to mix instanced and non-instanced object rendering.

### [03 - Indirect drawing](examples/indirectdraw/)
This example renders thousands of instanced objects with different geometries using only one single indirect draw call (if ```multiDrawIndirect``` is supported). Unlike direct drawing function, indirect drawing functions take their draw commands from a buffer object containing information like index count, index offset and number of instances to draw. Shows how to generate and render such an indirect draw command buffer that is staged to the device. Indirect draw buffers are the base for generating and updating draw commands on the GPU using shaders.

### [04 - Occlusion queries](examples/occlusionquery/)
Shows how to use occlusion queries to determine object visibility depending on the number of passed samples for a given object. Does an occlusion pass first, drawing all objects (and the occluder) with basic shaders, then reads the query results to conditionally color the objects during the final pass depending on their visibility.

### [05 - Pipeline statistics](examples/pipelinestatistics/)
```missing description```

## Physically based rendering

Physical based rendering as a lighting technique that achieves a more realistic and dynamic look by applying approximations of bidirectional reflectance distribution functions that rely on measured real-world material parameters and environment lighting.

### [01 - PBR basics](examples/pbrbasic/)
Basic implementation of a metallic-roughness based physical based rendering model using measured material parameters. Implements a specular BRDF based on material parameters for metallic reflectance, surface roughness and color and displays a grid of objects with varying metallic and roughness parameters light by multiple fixed light sources.

### [02 - PBR image based lighting](examples/pbribl/)
Adds ```image based lighting``` to the PBR equation. IBL uses the surrounding environment as a single light source. This adds an even more realistic look the models as the light contribution used by the materials is now controlled by the environment. The sample uses a fixed HDR environment cubemap as for lighting and reflectance. The new textures and cubemaps required for the enhanced lighting (BRDF 2D-LUT, irradiance cube and a filtered cube based on roughness) are generated at run-time based on that cubemap.

### [03 - PBR metallic-roughness workflow with IBL](examples/pbrtexture/)
This example adds a textured model with materials especially created for the metallic-roughness PBR workflow. Where the other examples used fixed material parameters for the PBR equation (metallic, roughness, albedo), this model contains texture maps that store these values (plus a normal and ambient occlusion map) used as input parameters for the BRDF shader. So even though the model uses only one material there are differing roughness and metallic areas and combined with image based lighting based on the environment the model is rendered with a realistic look.

## Deferred

These examples use a [deferred shading](https://en.wikipedia.org/wiki/Deferred_shading) setup.

### [01 - Deferred shading basics](examples/deferred/)
Demonstrates the use of multiple render targets to fill a G-Buffer for a deferred shading setup with multiple dynamic lights and normal mapped surfaces.

Deferred shading collects all values (color, normal, position) into different render targets in one pass thanks to multiple render targets, and then does all shading and lighting calculations based on these in screen space, thus allowing for much more light sources than traditional forward renderers.

### [02 - Deferred multi sampling](examples/deferredmultisampling/)
```missing description```

### [03 - Deferred shading shadow mapping](examples/deferredshadows/)
Building on the deferred shading setup this example adds directional shadows using shadow maps from multiple spotlights.

Scene depth from the different light's point-of-view is renderer to a layered depth attachment using only one pass. This is done using multiple geometry shader invocations that allows to output multiple instances of the same geometry using different matrices into the layers of the depth attachment.

The final scene compositing pass then samples from the layered depth map to determine if a fragment is shadowed or not.

### [04 - Screen space ambient occlusion](examples/ssao/)
Implements ambient occlusion in screen space, adding depth with the help of ambient occlusion to a scene. The example is using a deferred shading setup with the AO pass using the depth information from the deferred G-Buffer to generate the ambient occlusion values. A second pass is then applied to blur the AO results before they're applied to the scene in the final composition pass.

## Geometry shader

### [01 - Normal debugging](examples/geometryshader/)
Uses a geometry shader to generate per-vertex normals that could be used for debugging. The first pass displays the solid mesh using basic phong shading and then does a second pass with the geometry shader that generates normals for each vertex of the mesh.

### [02 - Viewport arrays](examples/viewportarray/)
```missing description```

## Tessellation shader

### [01 - Displacement mapping](examples/tessellation/)
Uses tessellation shaders to generate additional details from a low-poly mesh and displaces geometry based on a heightmap.

### [02 - Dynamic terrain tessellation](examples/terraintessellation/)
Renders a terrain with dynamic tessellation based on screen space triangle size, resulting in closer parts of the terrain getting more details than distant parts. The terrain geometry is also generated by the tessellation shader using a 16 bit height map for displacement. To improve performance the example also does frustum culling in the tessellation shader.

### [03 - PN-Triangles](examples/tessellation/)

Generating curved PN-Triangles on the GPU using tessellation shaders to add details to low-polygon meshes, based on [this paper](http://alex.vlachos.com/graphics/CurvedPNTriangles.pdf), with shaders from [this tutorial](http://onrendering.blogspot.de/2011/12/tessellation-on-gpu-curved-pn-triangles.html).

## Headless 

*Examples that run one-time tasks and don't make use of visual output (no window system integration). These can be run in environments where no user interface is available (see [blog entry](https://www.saschawillems.de/?p=2719) for details).*

### [01 - Render](examples/renderheadless)

Renders a basic scene to a (non-visible) frame buffer attachment, reads it back to host memory and stores it to disk. Also shows proper use of memory barriers required for device to host image synchronization.

### [02 - Compute](examples/computeheadless)

Demonstrates basic compute shader usage for running calculations on an input data set (passed via SSBO). A fibonacci row is calculated based on input data via the compute shader, stored back and displayed via command line.

## UI (?)

### [01 - Text rendering](examples/textoverlay/)
Renders a 2D text overlay on top of an existing 3D scene. The example implements a text overlay class with separate descriptor sets, layouts, pipelines and render pass to detach it from the rendering of the scene. The font is generated by loading glyph data from a [stb font file](http://nothings.org/stb/font/) into a buffer that's copied to the font image. After rendering the scene, the second render pass of the text overlay class loads the contents of the first render pass and displays text on top of it using blending.

### [02 - Distance field fonts](examples/distancefieldfonts/)
Instead of just sampling a bitmap font texture, a texture with per-character signed distance fields is used to generate high quality glyphs in the fragment shader. This results in a much higher quality than common bitmap fonts, even if heavily zoomed. Distance field font textures can be generated with tools like [Hiero](https://github.com/libgdx/libgdx/wiki/Hiero).

### [03 - ImGui overlay](examples/imgui/)
```missing description```


## Effects (?)

### [01 - Fullscreen radial blur](examples/radialblur/)

Demonstrates the basics of a fullscreen (fragment) shader effect. The scene is rendered into a low resolution offscreen framebuffer first and blended on top of the scene in a second pass. The fragment shader also applies a radial blur to it.

### [02 - Bloom](examples/bloom/)

Advanced fullscreen shader example implementing a separated gaussian blur using two passes. The glowing parts of the scene are rendered to a low-resolution offscreen framebuffer that is blurred in two steps and then blended on top of the scene.

### [03 - Parallax mapping](examples/parallaxmapping/)

Implements multiple texture mapping methods to simulate depth based purely on texture information without generating additional geometry. Along with basic normal mapping the example includes parallax mapping, steep parallax mapping and parallax occlusion mapping, with the later being the best in quality but also with the highest performance impact.

### [04 - Spherical environment mapping](examples/sphericalenvmapping/)

Uses a (spherical) material capture texture containing environment lighting and reflection information to fake complex lighting. The example also uses a texture array to store (and select) several material caps that can be toggled at runtime.

## Extensions

### [01 - Conservative rasterization (```VK_EXT_conservative_rasterization```)](examples/conservativeraster/)
```missing description```

### [02 - Push descriptors (```VK_KHR_push_descriptor```)](examples/pushdescriptors/)
```missing description```

### [03 - Debug markers (```VK_EXT_debug_marker```)](examples/debugmarker/)
Example application to be used along with [this tutorial](http://www.saschawillems.de/?page_id=2017) for demonstrating the use of the new VK_EXT_debug_marker extension. Introduced with Vulkan 1.0.12, it adds functionality to set debug markers, regions and name objects for advanced debugging in an offline graphics debugger like [RenderDoc](http://www.renderdoc.org).

## Misc

### [01 - Vulkan Gears](examples/gears/)

Vulkan interpretation of glxgears. Procedurally generates separate meshes for each gear, with every mesh having it's own uniform buffer object for animation. Also demonstrates how to use different descriptor sets.

### [02 - Vulkan demo scene](examples/vulkanscene/)

More of a playground than an actual example. Renders the Vulkan logo using multiple meshes with different shaders (and pipelines) including a background.
