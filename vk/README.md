# Vulkan C++ examples and demos

A comprehensive collection of open source C++ examples for [Vulkan®](https://www.khronos.org/vulkan/), the new graphics and compute API from Khronos.

[![Donate](https://img.shields.io/badge/Donate-PayPal-green.svg)](https://www.paypal.com/cgi-bin/webscr?cmd=_s-xclick&hosted_button_id=BHXPMV6ZKPH9E)

## Cloning
This repository contains submodules for external dependencies, so when doing a fresh clone you need to clone recursively:

```
git clone --recursive https://github.com/SaschaWillems/Vulkan.git
``` 

Existing repositories can be updated manually:

```
git submodule init
git submodule update
```

## Assets
Many examples require assets from the asset pack that is not part of this repository due to file size. A python script is included to download the asset pack that. Run

    python download_assets.py

from the root of the repository after cloning or see [this](data/README.md) for manual download.

## Building

The repository contains everything required to compile and build the examples on <img src="./images/windowslogo.png" alt="" height="22px" valign="bottom"> Windows, <img src="./images/linuxlogo.png" alt="" height="24px" valign="bottom"> Linux, <img src="./images/androidlogo.png" alt="" height="24px" valign="bottom"> Android, <img src="./images/applelogo.png" alt="" valign="bottom" height="24px"> iOS and macOS (using MoltenVK) using a C++ compiler that supports C++11.

See [BUILD.md](BUILD.md) for details on how to build for the different platforms.

## Examples

### Basics

#### [01 - Triangle](examples/triangle/)
**New Description**<br>
Basic and verbose example for getting a colored triangle rendered to the screen using Vulkan. This is meant as a starting point for learning Vulkan from the ground up. A huge part of the code is boilerplate that is abstracted away in later examples.

#### [02 - Pipelines](examples/pipelines/)
**New Description**<br>
Using pipeline state objects (pso) that bake state information (rasterization states, culling modes, etc.) along with the shaders into a single object, making it easy for an implementation to optimize usage (compared to OpenGL's dynamic state machine). Also demonstrates the use of pipeline derivatives.

#### [03 - Dynamic uniform buffers](examples/dynamicuniformbuffer/)
**New Description**<br>
Dynamic uniform buffers are used for rendering multiple objects with multiple matrices stored in a single uniform buffer object. Individual matrices are dynamically addressed upon descriptor binding time, minimizing the number of required descriptor sets.

#### [04 - Push constants](examples/pushconstants/)
**New Description**<br>
Uses push constants, small blocks of uniform data stored within a command buffer, to pass data to a shader without the need for uniform buffers.

#### [05 - Specialization constants](examples/specializationconstants/)
**New Description**<br>
Uses SPIR-V specialization constants to create multiple pipelines with different lighting paths from a single "uber" shader.

#### [06 - Texture mapping](examples/texture/)
**New Description**<br>
Loads a 2D texture from disk (including all mip levels), uses staging to upload it into video memory and samples from it using combined image samplers.

#### [07 - Cube map textures](examples/texturecubemap/)
**New Description**<br>
Loads a cube map texture from disk containing six different faces. All faces and mip levels are uploaded into video memory and the cubemap is sampled once as a skybox (for the background) and as a source for reflections (for a 3D model).

#### [08 - Texture arrays](examples/texturearray/)
**New Description**<br>
Loads a 2D texture array containing multiple 2D texture slices (each with it's own mip chain) and renders multiple meshes each sampling from a different layer of the texture. 2D texture arrays don't do any interpolation between the slices.

#### [09 - 3D textures](examples/texture3d/)
**New Description**<br>
Generates a 3D texture on the cpu (using perlin noise), uploads it to the device and samples it to render an animation. 3D textures store volumetric data and interpolate in all three dimensions.

#### [10 - Model rendering](examples/mesh/)
**New Description**<br>
Loads a 3D model and texture maps from a common file format (using [assimp](https://github.com/assimp/assimp)), uploads the vertex and index buffer data to video memory, sets up a matching vertex layout and renders the 3D model.

#### [11 - Sub passes](examples/subpasses/)
**New Description**<br>
Uses sub passes to read from previous framebuffer at the same pixel position within a single render pass. This is used for the deferred render composition with added forward transparency within a single renderpass.

#### [12 - Offscreen rendering](examples/offscreen/)
**New Description**<br>
Basic offscreen rendering in two passes. First pass renders the mirrored scene to a separate framebuffer with color and depth attachments, second pass samples from that color attachment for rendering a mirror surface.face.

#### [13 - CPU particle system](examples/particlefire/)
**New Description**<br>
Implements a simple cpu based particle system. Particle data is stored in host memory, updated on the CPU per-frame and synchronized with the device before it's rendered using pre-multiplied alpha.

#### [14 - Stencil buffer](examples/stencilbuffer/)
**New Description**<br>
Uses the stencil buffer and it's compare functionality for rendering a 3D model with dynamic outlines.

### Advanced

#### [01 - Scene rendering](examples/scenerendering/)
**New Description**<br>
Combines multiple techniques to render a complex scene consisting of multiple meshes, textures and materials. Meshes are stored and rendered from a single buffer using vertex offsets. Material parameters are passed via push constants , and separate per-model and scene descriptor sets are used to pass data to the shaders.

#### [02 - Multi sampling](examples/multisampling/)
**New Description**<br>
Implements multisample anti-aliasing (MSAA) using a renderpass with multisampled attachments and resolve attachments that get resolved into the visible frame buffer.

#### [03 - High dynamic range](examples/hdr/)
**New Description**<br>
Implements a high dynamic range rendering pipeline using 16/32 bit floating point precision for all internal formats, textures and calculations, including a bloom pass, manual exposure and tone mapping. 

#### [04 - Shadow mapping](examples/shadowmapping/)
**New Description**<br>
Rendering shadows for a directional light source. First pass stores depth values from the light's pov, second pass compares against these to check if a fragment is shadowed. Uses depth bias to avoid shadow artifacts and applies a PCF filer to smooth shadows.

#### [05 - Cascaded shadow mapping](examples/shadowmappingcascade/)
**New Description**<br>
Uses multiple shadow maps (stored as a layered texture) to increase shadow resolution for larger scenes. The camera frustum is split up into multiple cascades with corresponding layers in the shadow map. Layer selection for shadowing depth compare is then done by comparing fragment depth with the cascades' depths ranges.

#### [06 - Omnidirectional shadow mapping](examples/shadowmappingomni/)
**New Description**<br>
Uses a dynamic floating point cube map to implement shadowing for a point light source that casts shadows in all directions. The cube map is updated every frame and stores distance to the light source for each fragment used to determine if a fragment is shadowed.

#### [07 - Run-time mip-map generation](examples/texturemipmapgen/)
**New Description**<br>
Generating a complete mip-chain at runtime instead of loading it from a file, by blitting from one mip level, starting with the actual texture image, down to the next smaller size until the lower 1x1 pixel end of the mip chain.

#### [08 - Skeletal animation](examples/skeletalanimation/)
**New Description**<br>
Loads and renders an animated skinned 3D model. Skinning is done on the GPU by passing per-vertex bone weights and translation matrices. 

#### [09 - Capturing screenshots](examples/screenshot/)
**New Description**<br>
Capturing and saving an image after a scene has been rendered using blits to copy the last swapchain image from optimal device to host local linear memory, so that it can be stored into a ppm image.

### Compute shader

#### [01 - Image processing](examples/computeshader/)
**New Description**<br>
Uses a compute shader along with a separate compute queue to apply different convolution kernels (and effects) on an input image in realtime.

#### [02 - GPU particle system](examples/computeparticles/)
**New Description**<br>
Attraction based 2D GPU particle system using compute shaders. Particle data is stored in a shader storage buffer and only modified on the GPU using memory barriers for synchronizing compute particle updates with graphics pipeline vertex access.

#### [03 - N-body simulation](examples/computenbody/)
**New Description**<br>
N-body simulation based particle system with multiple attractors and particle-to-particle interaction using two passes separating particle movement calculation and final integration. Shared compute shader memory is used to speed up compute calculations.

#### [04 - Ray tracing](examples/raytracing/)
**New Description**<br>
Simple GPU ray tracer with shadows and reflections using a compute shader. No scene geometry is rendered in the graphics pass.

#### [05 - Cloth simulation](examples/computecloth/)
**New Description**<br>
Mass-spring based cloth system on the GPU using a compute shader to calculate and integrate spring forces, also implementing basic collision with a fixed scene object.

#### [06 - Cull and LOD](examples/computecullandlod/)
**New Description**<br>
Purely GPU based frustum visibility culling and level-of-detail system. A compute shader is used to modify draw commands stored in an indirect draw commands buffer to toggle model visibility and select it's level-of-detail based on camera distance, no calculations have to be done on and synced with the CPU.

### Performance

#### [01 - Multi threaded command buffer generation](examples/multithreading/)
**New Description**<br>
Multi threaded parallel command buffer generation. Instead of prebuilding and reusing the same command buffers this sample uses multiple hardware threads to demonstrate parallel per-frame recreation of secondary command buffers that are executed and submitted in a primary buffer once all threads have finished.

#### [02 - Instancing](examples/instancing/)
**New Description**<br>
Uses the instancing feature for rendering many instances of the same mesh from a single vertex buffer with variable parameters and textures (indexing a layered texture). Instanced data is passed using a secondary vertex buffer.

#### [03 - Indirect drawing](examples/indirectdraw/)
**New Description**<br>
Rendering thousands of instanced objects with different geometry using one single indirect draw call instead of issuing separate draws. All draw commands to be executed are stored in a dedicated indirect draw buffer object (storing index count, offset, instance count, etc.) that is uploaded to the device and sourced by the indirect draw command for rendering.

#### [04 - Occlusion queries](examples/occlusionquery/)
**New Description**<br>
Using query pool objects to get number of passed samples for rendered primitives got determining on-screen visibility.

#### [05 - Pipeline statistics](examples/pipelinestatistics/)
**New Description**<br>
Using query pool objects to gather statistics from different stages of the pipeline like vertex, fragment shader and tessellation evaluation shader invocations depending on payload.

### Physically based rendering

Physical based rendering as a lighting technique that achieves a more realistic and dynamic look by applying approximations of bidirectional reflectance distribution functions based on measured real-world material parameters and environment lighting.

#### [01 - PBR basics](examples/pbrbasic/)
**New Description**<br>
Demonstrates a basic specular BRDF implementation with solid materials and fixed light sources on a grid of objects with varying material parameters, demonstrating how metallic reflectance and surface roughness affect the appearance of pbr lit objects.

#### [02 - PBR image based lighting](examples/pbribl/)
**New Description**<br>
Adds image based lighting from an hdr environment cubemap to the PBR equation, using the surrounding environment as the light source. This adds an even more realistic look the scene as the light contribution used by the materials is now controlled by the environment. Also shows how to generate the BRDF 2D-LUT and irradiance and filtered cube maps from the environment map.

#### [03 - Textured PBR with IBL](examples/pbrtexture/)
**New Description**<br>
Renders a model specially crafted for a metallic-roughness PBR workflow with textures defining material parameters for the PRB equation (albedo, metallic, roughness, baked ambient occlusion, normal maps) in an image based lighting environment.

### Deferred

These examples use a [deferred shading](https://en.wikipedia.org/wiki/Deferred_shading) setup.

#### [01 - Deferred shading basics](examples/deferred/)
**New Description**<br>
Uses multiple render targets to fill all attachments (albedo, normals, position, depth) required for a G-Buffer in a single pass. A deferred pass then uses these to calculate shading and lighting in screen space, so that calculations only have to be done for visible fragments independent of no. of lights.

#### [02 - Deferred multi sampling](examples/deferredmultisampling/)
**New Description**<br>
Adds multi sampling to a deferred renderer using manual resolve in the fragment shader.

#### [03 - Deferred shading shadow mapping](examples/deferredshadows/)
**New Description**<br>
Adds shadows from multiple spotlights to a deferred renderer using a layered depth attachment filled in one pass using multiple geometry shader invocations.

#### [04 - Screen space ambient occlusion](examples/ssao/)
**New Description**<br>
Adds ambient occlusion in screen space to a 3D scene. Depth values from a previous deferred pass are used to generate an ambient occlusion texture that is blurred before being applied to the scene in a final composition path.

### Geometry shader

#### [01 - Normal debugging](examples/geometryshader/)
**New Description**<br>
Visualizing per-vertex model normals (for debugging). First pass renders the plain model, second pass uses a geometry shader to generate colored lines based on per-vertex model normals,

#### [02 - Viewport arrays](examples/viewportarray/)
**New Description**<br>
Renders a scene to multiple viewports in one pass using a geometry shader to apply different matrices per viewport to simulate stereoscopic rendering (left/right). Requires a device with support for ```multiViewport```.

### Tessellation shader

#### [01 - Displacement mapping](examples/tessellation/)
**New Description**<br>
Uses a height map to dynamically generate and displace additional geometric detail for a low-poly mesh.

#### [02 - Dynamic terrain tessellation](examples/terraintessellation/)
**New Description**<br>
Renders a terrain using tessellation shaders for height displacement (based on a 16-bit height map), dynamic level-of-detail (based on triangle screen space size) and per-patch frustum culling.

#### [03 - Model tessellation](examples/tessellation/)
**New Description**<br>
Uses curved PN-triangles ([paper](http://alex.vlachos.com/graphics/CurvedPNTriangles.pdf)) for adding details to a low-polygon model.

### Headless 

Examples that run one-time tasks and don't make use of visual output (no window system integration). These can be run in environments where no user interface is available ([blog entry](https://www.saschawillems.de/?p=2719)).

#### [01 - Render](examples/renderheadless)
**New Description**<br>
Renders a basic scene to a (non-visible) frame buffer attachment, reads it back to host memory and stores it to disk without any on-screen presentation, showing proper use of memory barriers required for device to host image synchronization.

#### [02 - Compute](examples/computeheadless)
**New Description**<br>
Only uses compute shader capabilities for running calculations on an input data set (passed via SSBO). A fibonacci row is calculated based on input data via the compute shader, stored back and displayed via command line.

### User interface

#### [01 - Text rendering](examples/textoverlay/)
**New Description**<br>
Load and render a 2D text overlay created from the bitmap glyph data of a [stb font file](https://nothings.org/stb/font/). This data is uploaded as a texture and used for displaying text on top of a 3D scene in a second pass.

#### [02 - Distance field fonts](examples/distancefieldfonts/)
**New Description**<br>
Uses a texture that stores signed distance field information per character along with a special fragment shader calculating output based on that distance data. This results in crisp high quality font rendering independent of font size and scale.

#### [03 - ImGui overlay](examples/imgui/)
**New Description**<br>
Generates and renders a complex user interface with multiple windows, controls and user interaction on top of a 3D scene. The UI is generated using [Dear ImGUI](https://github.com/ocornut/imgui) and updated each frame.

### Effects

#### [01 - Fullscreen radial blur](examples/radialblur/)
**New Description**<br>
Demonstrates the basics of fullscreen shader effects. The scene is rendered into an offscreen framebuffer at lower resolution and rendered as a fullscreen quad atop the scene using a radial blur fragment shader.

#### [02 - Bloom](examples/bloom/)
**New Description**<br>
Advanced fullscreen effect example adding a bloom effect to a scene. Glowing scene parts are rendered to a low res offscreen framebuffer that is applied atop the scene using a two pass separated gaussian blur.

#### [03 - Parallax mapping](examples/parallaxmapping/)
**New Description**<br>
Implements multiple texture mapping methods to simulate depth based on texture information: Normal mapping, parallax mapping, steep parallax mapping and parallax occlusion mapping (best quality, worst performance).

#### [04 - Spherical environment mapping](examples/sphericalenvmapping/)
**New Description**<br>
Uses a spherical material capture texture array defining environment lighting and reflection information to fake complex lighting. 

### Extensions

#### [01 - Conservative rasterization (VK_EXT_conservative_rasterization)](examples/conservativeraster/)
**New Description**<br>
Uses conservative rasterization to change the way fragments are generated by the gpu. The example enables overestimation to generate fragments for every pixel touched instead of only pixels that are fully covered ([blog post](https://www.saschawillems.de/?p=2778)).

#### [02 - Push descriptors (VK_KHR_push_descriptor)](examples/pushdescriptors/)
**New Description**<br>
Uses push descriptors apply the push constants concept to descriptor sets. Instead of creating per-object descriptor sets for rendering multiple objects, this example passes descriptors at command buffer creation time.

#### [03 - Debug markers (VK_EXT_debug_marker)](examples/debugmarker/)
**New Description**<br>
Uses the VK_EXT_debug_marker extension to set debug markers, regions and to name Vulkan objects for advanced debugging in graphics debuggers like [RenderDoc](https://www.renderdoc.org). Details can be found in [this tutorial](https://www.saschawillems.de/?page_id=2017).

### Misc

#### [01 - Vulkan Gears](examples/gears/)
**New Description**<br>
Vulkan interpretation of glxgears. Procedurally generates and animates multiple gears.

#### [02 - Vulkan demo scene](examples/vulkanscene/)
**New Description**<br>
Renders a Vulkan demo scene with logos and mascots. Not an actual example but more of a playground and showcase.

## Credits and attributions
See [CREDITS.md](CREDITS.md) for additional credits and attributions.