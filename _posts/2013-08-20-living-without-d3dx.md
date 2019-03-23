---
layout: post
title: Living without D3DX
date: 2013-08-20 17:49
author: Chuck Walbourn
comments: true
categories: [direct3d, dxsdk]
---
Over the past few years, I've been working on a number of projects in part motivated by the need to replace legacy D3DX functionality. As noted on <a href="https://docs.microsoft.com/en-us/windows/desktop/directx-sdk--august-2009-">Microsoft Docs</a>, all versions of D3DX are deprecated and are not shipped with the Windows 8.x SDK. This includes D3DX9, D3DX10, and D3DX11. There are plenty of options for moving existing code over to newer, more supportable solutions most of which are now shared-source.
<!--more-->

I've built a number of porting notes tables for each of these topics, and then realized today I don't have a single place to see it all. So here it is, the "D3DX porting mega-table" for Direct3D 11.

<strong>Direct3D 9:</strong> There are not many good replacements for the functionality in D3DX9 if you are still using Direct3D 9. You can make use of D3DCompile APIs directly with the legacy DirectX SDK, but newer versions of the D3DCompile DLL in the Windows 8.x SDK are not compatible with Windows XP for runtime use. You can use DirectXMath/XNAMath for Direct3D 9 instead of D3DXMath. The <a href="https://walbourn.github.io/ddswithoutd3dx-sample-update/">DDSWithoutD3DX</a> sample supports Direct3D 9, and you can use <a href="https://walbourn.github.io/directxtex/">DirectXTex</a> and <a href="https://walbourn.github.io/directxmesh/">DirectXMesh</a> when preparing content for use by Direct3D 9.

<strong>Direct3D 10:</strong> The best option for Direct3D 10.x applications is to update them to use Direct3D 11, and then utilize all the options here to replace legacy D3XD10. Porting the API is very <a href="https://docs.microsoft.com/en-us/windows/desktop/direct3d11/d3d11-programming-guide-migrating">straightforward</a>, and Direct3D 11 is <a href="https://docs.microsoft.com/en-us/windows/desktop/direct3darticles/direct3d11-deployment">available</a> on all supported platforms that have Direct3D 10.

<strong>Direct3D 12: </strong>There is a header that ships in the DirectX 12 samples on <a href="https://github.com/Microsoft/DirectX-Graphics-Samples/tree/master/Libraries/D3DX12">GitHub</a> called <code>d3dx12.h</code>. It is all inline utility header which has no DLL or redistributable dependency, and can be used under the MIT license. It is not included in the Windows SDK. If you are looking for additional functionality, see the <a href="https://walbourn.github.io/directx-tool-kit-for-directx-12/">DirectX Tool Kit for DirectX 12</a>, <a href="https://walbourn.github.io/directxtex-and-directxmesh-now-support-direct3d-12/">DirectXTex and DirectXMesh</a>.

<strong>Related:</strong> <a href="https://walbourn.github.io/where-is-the-directx-sdk/">Where is the DirectX SDK?</a>, <a href="https://walbourn.github.io/where-is-the-directx-sdk-2013-edition/">Where is the DirectX SDK (2013 Edition)?</a>, <a href="https://walbourn.github.io/where-is-the-directx-sdk-2015-edition/">Where is the DirectX SDK (2015 Edition)?</a>, <a href="https://walbourn.github.io/directx-sdk-s-of-a-certain-age/">DirectX SDKs of a certain age</a>, <a href="https://walbourn.github.io/dxut-for-win32-desktop-update/">DXUT for Win32 Desktop Update</a>, <a href="https://walbourn.github.io/directx-sdk-samples-catalog/">DirectX SDK Samples Catalog</a>, <a href="https://walbourn.github.io/directx-sdk-tools-catalog/">DirectX SDK Tools Catalog</a>

<strong>CodePlex: </strong>All CodePlex links below will redirect to the Microsoft organizational section of GitHub as CodePlex has been <a href="https://devblogs.microsoft.com/bharry/shutting-down-codeplex">shutdown</a>.

<h1>General Helpers</h1>

The <a href="https://walbourn.github.io/directxtk/">DirectX Tool Kit</a> provides a number of helpers that are designed to simplify Direct3D 11 programming in the tradition of the original D3DX library. You can find the library on <a href="http://go.microsoft.com/fwlink/?LinkId=248929">CodePlex</a> and <a href="https://github.com/Microsoft/DirectXTK">GitHub</a>.

<table border="1">
<tbody>
<tr>
<td><code>ID3DX10Sprite</code></td>
<td><code>SpriteBatch</code></td>
</tr>
<tr>
<td><code>ID3DX10Font</code></td>
<td><code>SpriteFont, SpriteBatch</code></td>
</tr>
<tr>
<td><code>ID3DX10Mesh</code></td>
<td><code>Model, ModelMesh, ModelMeshPart</code></td>
</tr>
<tr>
<td><code>D3DXCreateBox
D3DXCreateCylinder
D3DXCreateSphere
D3DXCreateTeapot
D3DXCreateTorus</code></td>
<td><code>GeometricPrimitive</code></td>
</tr>
<tr>
<td><code>D3DX11CreateShaderResourceViewFromFile</code></td>
<td><code>CreateXXXTextureFromFile</code></td>
</tr>
<tr>
<td><code>D3DX11CreateShaderResourceViewFromResource
D3DX11CreateShaderResourceViewFromMemory</code></td>
<td><code>CreateXXXTextureFromMemory</code></td>
</tr>
<tr>
<td><code>D3DX11CreateTextureFromFile</code></td>
<td><code>CreateXXXTextureFromFile</code></td>
</tr>
<tr>
<td><code>D3DX11CreateTextureFromResource
D3DX11CreateTextureFromMemory </code></td>
<td><code>CreateXXXTextureFromMemory</code></td>
</tr>
<tr>
<td><code>D3DX11SaveTextureToFile</code></td>
<td><code>SaveXXXTextureToFile</code></td>
</tr>
</tbody>
</table>

The DXERR library is another helper that shipped in the legacy DirectX SDK. In Windows 8.x or later, you can just use <code>FormatMessage</code> to use the built-in error descriptions which support DirectX. Otherwise, the best replacement is to compile your own version of DXERR.

<table border="1">
<tbody>
<tr>
<td><code>DXGetErrorString
DXGetErrorDescription
DXTrace</code></td>
<td>See <a href="https://walbourn.github.io/wheres-dxerr-lib/">Where's DXERR.LIB?</a></td>
</tr>
</tbody>
</table>

<h1>HLSL Functions</h1>

The HLSL compiler, shader reflection API, and related functionality has been broken out into it's own <a href="https://walbourn.github.io/hlsl-fxc-and-d3dcompile/">D3DCompile</a> DLL for some time. D3DCompile is in the Windows 8.x SDK and is included with VS 2012 and VS 2013 Preview.
<table border="1">
<tbody>
<tr>
<td><code>D3DXCompileShaderFromFile
D3DX10CompileFromFile
D3DX11CompileFromFile</code></td>
<td><a href="https://docs.microsoft.com/en-us/windows/desktop/api/d3dcompiler/nf-d3dcompiler-d3dcompilefromfile">D3DCompileFromFile</a></td>
</tr>
<tr>
<td><code>D3DXCompileShader
D3D10CompileShader
D3DX10CompileFromMemory
D3DX11CompileFromMemory</code></td>
<td><a href="https://docs.microsoft.com/en-us/windows/desktop/api/d3dcompiler/nf-d3dcompiler-d3dcompile">D3DCompile</a></td>
</tr>
<tr>
<td><code>D3DXCompileShaderFromResource
D3DX10CompileFromResource
D3DX11CompileFromResource</code></td>
<td>No direct equivalent. Can use resource APIs and then <code>D3DCompile</code> above.</td>
</tr>
<tr>
<td><code>D3DXPreprocessShader
D3DXPreprocessShaderFromFile
D3DXPreprocessShaderFromResource
D3D10PreprocessShader
D3DX10PreprocessShaderFromFile
D3DX10PreprocessShaderFromMemory
D3DX10PreprocessShaderFromResource
D3DX11PreprocessShaderFromFile
D3DX11PreprocessShaderFromMemory
D3DX11PreprocessShaderFromResource</code></td>
<td><a href="https://docs.microsoft.com/en-us/windows/desktop/api/d3dcompiler/nf-d3dcompiler-d3dpreprocess">D3DPreprocess</a></td>
</tr>
<tr>
<td><code>D3DXDisassembleShader
D3D10DisassembleShader
D3DX10DisassembleShader</code></td>
<td><a href="https://docs.microsoft.com/en-us/windows/desktop/api/d3dcompiler/nf-d3dcompiler-d3ddisassemble">D3DDisassemble</a></td>
</tr>
<tr>
<td><code>D3D10ReflectShader
D3DX10ReflectShader</code></td>
<td><a href="https://docs.microsoft.com/en-us/windows/desktop/api/d3dcompiler/nf-d3dcompiler-d3dreflect">D3DReflect</a>
<a href="https://docs.microsoft.com/en-us/windows/desktop/direct3dhlsl/d3d11reflect">D3D11Reflect</a></td>
</tr>
<tr>
<td><code>ID3DXBuffer
ID3D10Blob</code></td>
<td><a href="http://msdn.microsoft.com/en-us/library/windows/desktop/ff728743.aspx">ID3DBlob</a></td>
</tr>
<tr>
<td><code>D3DXCreateBuffer
D3D10CreateBlob</code></td>
<td><a href="https://docs.microsoft.com/en-us/windows/desktop/api/d3dcompiler/nf-d3dcompilerd3dcreateblob">D3DCreateBlob</a></td>
</tr>
<tr>
<td><code>D3D10GetInputSignatureBlob
D3D10GetOutputSignatureBlob
D3D10GetInputAndOutputSignatureBlob
D3D10GetShaderDebugInfo</code></td>
<td><a href="https://docs.microsoft.com/en-us/windows/desktop/api/d3dcompiler/nf-d3dcompiler-d3dgetblobpart">D3DGetBlobPart</a></td>
</tr>
</tbody>
</table>

<h1>Texture Functions</h1>

The <a href="https://walbourn.github.io/directxtex/">DirectXTex</a> library is primarily intended for <a href="https://walbourn.github.io/direct3d-11-textures-and-block-compression/">texture processing</a> offline with tools, although it can also be used at runtime for doing texture block compression, mipmap generation, or handling more general image processing needs. <a href="https://walbourn.github.io/directxtk/">DirectXTK</a> is intended for use at runtime with light-weight helpers and image loaders, but does not provide support for general runtime texture format conversion. You can find both libraries on <a href="http://go.microsoft.com/fwlink/?LinkId=248926">CodePlex</a> and <a href="https://github.com/Microsoft/DirectXTex">GitHub</a>.

<table border="1">
<tbody>
<tr>
<td><code>D3DX11ComputeNormalMap</code></td>
<td>DirectXTex library, <code> ComputeNormalMap</code></td>
</tr>
<tr>
<td><code>D3DX11CreateShaderResourceViewFromFile
D3DX11CreateTextureFromFile</code></td>
<td>DDSTextureLoader: <code>CreateDDSTextureFromFile</code>
WICTextureLoader: <code>CreateWICTextureFromFile</code>DirectXTex library (tools), <code>LoadFromXXXFile</code> then <code>CreateShaderResourceView</code> or <code>CreateTexture</code></td>
</tr>
<tr>
<td><code>D3DX11CreateShaderResourceViewFromMemory
D3DX11CreateTextureFromMemory</code></td>
<td>DDSTextureLoader: <code>CreateDDSTextureFromMemory</code>
WICTextureLoader: <code>CreateWICTextureFromMemory</code>DirectXTex library (tools), <code>LoadFromXXXMemory</code> then <code>CreateShaderResourceView</code> or <code>CreateTexture</code></td>
</tr>
<tr>
<td><code>D3DX11CreateShaderResourceViewFromResource
D3DX11CreateTextureFromResource</code></td>
<td>No direct equivalent, can use Win32 resource functions and then the 'from memory' APIs above.</td>
</tr>
<tr>
<td><code>D3DX11FilterTexture</code></td>
<td>DirectXTex library, <code>GenerateMipMaps</code> and <code>GenerateMipMaps3D</code></td>
</tr>
<tr>
<td><code>D3DX11GetImageInfoFromFile</code></td>
<td>DirectXTex library, <code>GetMetadataFromXXXFile</code></td>
</tr>
<tr>
<td><code>D3DX11GetImageInfoFromMemory</code></td>
<td>DirectXTex library, <code>GetMetadataFromXXXMemory</code></td>
</tr>
<tr>
<td><code>D3DX11GetImageInfoFromResource</code></td>
<td>No direct equivalent, can use Win32 resource functions and then the 'from memory' APIs above.</td>
</tr>
<tr>
<td><code>D3DX11LoadTextureFromTexture</code></td>
<td>DirectXTex library, <code>Resize</code>, <code>Convert</code>, <code>Compress</code>, <code>Decompress</code>, and/or <code>CopyRectangle</code></td>
</tr>
<tr>
<td><code>D3DX11SaveTextureToFile</code></td>
<td>ScreenGrab: <code>SaveDDSTextureToFile</code> or <code>SaveWICTextureToFile</code>

DirectXTex library, <code>CaptureTexture</code> then <code>SaveToXXXFile</code></td>
</tr>
<tr>
<td><code>D3DX11SaveTextureToMemory</code></td>
<td>DirectXTex library, <code>CaptureTexture</code> then <code>SaveToXXXMemory</code></td>
</tr>
</tbody>
</table>

<h1>Geometry Functions</h1>

The <a href="https://walbourn.github.io/directxmesh/">DirectXMesh</a> library is intended for geometry processing offline with tools, although it can be used at runtime. You can find the library on <a href="http://go.microsoft.com/fwlink/?LinkID=324981">CodePlex</a> and <a href="https://github.com/Microsoft/DirectXMesh">GitHub</a>.

<table border="1">
<tbody>
<tr>
<td><code>D3DXCleanMesh</code></td>
<td><code>Clean</code></td>
</tr>
<tr>
<td><code>D3DXComputeNormals</code></td>
<td><code>ComputeNormals</code></td>
</tr>
<tr>
<td><code>D3DXComputeTangent
D3DXComputeTangentFrame
D3DXComputeTangentFrameEx</code></td>
<td><code>ComputeTangentFrame</code></td>
</tr>
<tr>
<td><code>ID3DX10Mesh::GenerateAdjacencyAndPointReps</code></td>
<td><code>GenerateAdjacencyAndPointReps</code></td>
</tr>
<tr>
<td><code>ID3DX10Mesh::GenerateGSAdjacency</code></td>
<td><code>GenerateGSAdjacency</code></td>
</tr>
<tr>
<td><code>ID3DX10Mesh::Optimize</code></td>
<td><code>AttributeSort
OptimizeFacesEx
OptimizeVertices
ReorderIB
FinalizeIB
FinalizeVB</code></td>
</tr>
<tr>
<td><code>D3DXOptimizeFaces</code></td>
<td><code>OptimizeFaces</code></td>
</tr>
<tr>
<td><code>D3DXOptimizeVertices</code></td>
<td><code>OptimizeVertices</code></td>
</tr>
<tr>
<td><code>D3DXValidMesh</code></td>
<td><code>Validate</code></td>
</tr>
<tr>
<td><code>D3DXWeldVertices</code></td>
<td><code>WeldVertices
CompactVB</code></td>
</tr>
</tbody>
</table>

<h1>Math</h1>

The legacy D3DXMath library has been replaced by <a href="https://walbourn.github.io/introducing-directxmath/">DirectXMath</a>. The library is in the Windows 8.x SDK or later, as well as being available on <a href="https://github.com/Microsoft/DirectXMath">GitHub</a>. You may also want to take a look at the <a href="https://github.com/Microsoft/DirectXTK/wiki/SimpleMath">SimpleMath </a>wrapper.

<table border="1">
<tbody>
<tr>
<td><code>D3DXFLOAT16</code></td>
<td>``HALF``</td>
</tr>
<tr>
<td><code>D3DXMATRIXA16</code></td>
<td>``XMMATRIX`` or ``XMFLOAT4X4A``</td>
</tr>
<tr>
<td><code>D3DXMATRIX</code></td>
<td>``XMFLOAT4X4``</td>
</tr>
<tr>
<td><code>D3DXQUATERNION</code>

<code>D3DXPLANE</code>

<code>D3DXCOLOR</code></td>
<td>``XMVECTOR`` is used rather than having unique types, so you will likely need to use an ``XMFLOAT4``</td>
</tr>
<tr>
<td><code>D3DXVECTOR2</code></td>
<td>``XMFLOAT2``</td>
</tr>
<tr>
<td><code>D3DXVECTOR2_16F</code></td>
<td>``XMHALF2``</td>
</tr>
<tr>
<td><code>D3DXVECTOR3</code></td>
<td>``XMFLOAT3``</td>
</tr>
<tr>
<td><code>D3DXVECTOR4</code></td>
<td>``XMFLOAT4``

(or if you can guarantee the data is 16-byte aligned, ``XMVECTOR`` or ``XMFLOAT4A``)</td>
</tr>
<tr>
<td><code>D3DXVECTOR4_16F</code></td>
<td>``XMHALF4``</td>
</tr>
<tr>
<td><code>D3DX_PI</code></td>
<td>``XM_PI``</td>
</tr>
<tr>
<td><code>D3DX_1BYPI</code></td>
<td>``XM_1DIVPI``</td>
</tr>
<tr>
<td><code>D3DXToRadian</code></td>
<td>``XMConvertToRadians``</td>
</tr>
<tr>
<td><code>D3DXToDegree</code></td>
<td>``XMConvertToDegrees``</td>
</tr>
<tr>
<td><code>D3DXBoxBoundProbe</code></td>
<td>``BoundingBox::Intersects(XMVECTOR, XMVECTOR, float&)``</td>
</tr>
<tr>
<td><code>D3DXComputeBoundingBox</code></td>
<td>``BoundingBox::CreateFromPoints``</td>
</tr>
<tr>
<td><code>D3DXComputeBoundingSphere</code></td>
<td>``BoundingSphere::CreateFromPoints``</td>
</tr>
<tr>
<td><code>D3DXSphereBoundProbe</code></td>
<td>``BoundingSphere::Intersects(XMVECTOR, XMVECTOR, float&)``</td>
</tr>
<tr>
<td><code>D3DXIntersectTriFunction</code></td>
<td>``TriangleTests::Intersects``</td>
</tr>
<tr>
<td><code>D3DXFloat32To16Array</code></td>
<td>``XMConvertFloatToHalfStream``</td>
</tr>
<tr>
<td><code>D3DXFloat16To32Array</code></td>
<td>``XMConvertHalfToFloatStream``</td>
</tr>
<tr>
<td><code>D3DXVec2Length</code></td>
<td>``XMVector2LengthEst``</td>
</tr>
<tr>
<td><code>D3DXVec2LengthSq</code></td>
<td>``XMVector2LengthSq``</td>
</tr>
<tr>
<td><code>D3DXVec2Dot</code></td>
<td>``XMVector2Dot``</td>
</tr>
<tr>
<td><code>D3DXVec2CCW</code></td>
<td>``XMVector2Cross``</td>
</tr>
<tr>
<td><code>D3DXVec2Add</code></td>
<td>``XMVectorAdd``</td>
</tr>
<tr>
<td><code>D3DXVec2Subtract</code></td>
<td>``XMVectorSubtract``</td>
</tr>
<tr>
<td><code>D3DXVec2Minimize</code></td>
<td>``XMVectorMin``</td>
</tr>
<tr>
<td><code>D3DXVec2Maximize</code></td>
<td>``XMVectorMax``</td>
</tr>
<tr>
<td><code>D3DXVec2Scale</code></td>
<td>``XMVectorScale``</td>
</tr>
<tr>
<td><code>D3DXVec2Lerp</code></td>
<td>``XMVectorLerpV``</td>
</tr>
<tr>
<td><code>D3DXVec2Normalize</code></td>
<td>``XMVector2NormalizeEst``</td>
</tr>
<tr>
<td><code>D3DXVec2Hermite</code></td>
<td>``XMVectorHermiteV``</td>
</tr>
<tr>
<td><code>D3DXVec2CatmullRom</code></td>
<td>``XMVectorCatmullRomV``</td>
</tr>
<tr>
<td><code>D3DXVec2BaryCentric</code></td>
<td>``XMVectorBaryCentricV``</td>
</tr>
<tr>
<td><code>D3DXVec2Transform</code></td>
<td>``XMVector2Transform``</td>
</tr>
<tr>
<td><code>D3DXVec2TransformCoord</code></td>
<td>``XMVector2TransformCoord``</td>
</tr>
<tr>
<td><code>D3DXVec2TransformNormal</code></td>
<td>``XMVector2TransformNormal``</td>
</tr>
<tr>
<td><code>D3DXVec2TransformArray</code></td>
<td>``XMVector2TransformStream``</td>
</tr>
<tr>
<td><code>D3DXVec2TransformCoordArray</code></td>
<td>``XMVector2TransformCoordStream``</td>
</tr>
<tr>
<td><code>D3DXVec2TransformNormalArray</code></td>
<td>``XMVector2TransformNormalStream``</td>
</tr>
<tr>
<td><code>D3DXVec3Length</code></td>
<td>``XMVector3LengthEst``</td>
</tr>
<tr>
<td><code>D3DXVec3LengthSq</code></td>
<td>``XMVector3LengthSq``</td>
</tr>
<tr>
<td><code>D3DXVec3Dot</code></td>
<td>``XMVector3Dot``</td>
</tr>
<tr>
<td><code>D3DXVec3Cross</code></td>
<td>``XMVector3Cross``</td>
</tr>
<tr>
<td><code>D3DXVec3Add</code></td>
<td>``XMVectorAdd``</td>
</tr>
<tr>
<td><code>D3DXVec3Subtract</code></td>
<td>``XMVectorSubtract``</td>
</tr>
<tr>
<td><code>D3DXVec3Minimize</code></td>
<td>``XMVectorMin``</td>
</tr>
<tr>
<td><code>D3DXVec3Maximize</code></td>
<td>``XMVectorMax``</td>
</tr>
<tr>
<td><code>D3DXVec3Scale</code></td>
<td>``XMVectorScale``</td>
</tr>
<tr>
<td><code>D3DXVec3Lerp</code></td>
<td>``XMVectorLerpV``</td>
</tr>
<tr>
<td><code>D3DXVec3Normalize</code></td>
<td>``XMVector3NormalizeEst``</td>
</tr>
<tr>
<td><code>D3DXVec3Hermite</code></td>
<td>``XMVectorHermiteV``</td>
</tr>
<tr>
<td><code>D3DXVec3CatmullRom</code></td>
<td>``XMVectorCatmullRomV``</td>
</tr>
<tr>
<td><code>D3DXVec3BaryCentric</code></td>
<td>``XMVectorBaryCentricV``</td>
</tr>
<tr>
<td><code>D3DXVec3Transform</code></td>
<td>``XMVector3Transform``</td>
</tr>
<tr>
<td><code>D3DXVec3TransformCoord</code></td>
<td>``XMVector3TransformCoord``</td>
</tr>
<tr>
<td><code>D3DXVec3TransformNormal</code></td>
<td>``XMVector3TransformNormal``</td>
</tr>
<tr>
<td><code>D3DXVec3TransformArray</code></td>
<td>``XMVector3TransformStream``</td>
</tr>
<tr>
<td><code>D3DXVec3TransformCoordArray</code></td>
<td>``XMVector3TransformCoordStream``</td>
</tr>
<tr>
<td><code>D3DXVec3TransformNormalArray</code></td>
<td>``XMVector3TransformNormalStream``</td>
</tr>
<tr>
<td><code>D3DXVec3Project</code></td>
<td>``XMVector3Project``</td>
</tr>
<tr>
<td><code>D3DXVec3Unproject</code></td>
<td>``XMVector3Unproject``</td>
</tr>
<tr>
<td><code>D3DXVec3ProjectArray</code></td>
<td>``XMVector3ProjectStream``</td>
</tr>
<tr>
<td><code>D3DXVec3UnprojectArray</code></td>
<td>``XMVector3UnprojectStream``</td>
</tr>
<tr>
<td><code>D3DXVec4Length</code></td>
<td>``XMVector4LengthEst``</td>
</tr>
<tr>
<td><code>D3DXVec4LengthSq</code></td>
<td>``XMVector4LengthSq``</td>
</tr>
<tr>
<td><code>D3DXVec4Dot</code></td>
<td>``XMVector4Dot``</td>
</tr>
<tr>
<td><code>D3DXVec4Add</code></td>
<td>``XMVectorAdd``</td>
</tr>
<tr>
<td><code>D3DXVec4Subtract</code></td>
<td>``XMVectorSubtract``</td>
</tr>
<tr>
<td><code>D3DXVec4Minimize</code></td>
<td>``XMVectorMin``</td>
</tr>
<tr>
<td><code>D3DXVec4Maximize</code></td>
<td>``XMVectorMax``</td>
</tr>
<tr>
<td><code>D3DXVec4Scale</code></td>
<td>``XMVectorScale``</td>
</tr>
<tr>
<td><code>D3DXVec4Lerp</code></td>
<td>``XMVectorLerpV``</td>
</tr>
<tr>
<td><code>D3DXVec4Cross</code></td>
<td>``XMVector4Cross``</td>
</tr>
<tr>
<td><code>D3DXVec4Normalize</code></td>
<td>``XMVector4NormalizeEst``</td>
</tr>
<tr>
<td><code>D3DXVec4Hermite</code></td>
<td>``XMVectorHermiteV``</td>
</tr>
<tr>
<td><code>D3DXVec4CatmullRom</code></td>
<td>``XMVectorCatmullRomV``</td>
</tr>
<tr>
<td><code>D3DXVec4BaryCentric</code></td>
<td>``XMVectorBaryCentricV``</td>
</tr>
<tr>
<td><code>D3DXVec4Transform</code></td>
<td>``XMVector4Transform``</td>
</tr>
<tr>
<td><code>D3DXVec4TransformArray</code></td>
<td>``XMVector4TransformStream``</td>
</tr>
<tr>
<td><code>D3DXMatrixIdentity</code></td>
<td>``XMMatrixIdentity``</td>
</tr>
<tr>
<td><code>D3DXMatrixDeterminant</code></td>
<td>``XMMatrixDeterminant``</td>
</tr>
<tr>
<td><code>D3DXMatrixDecompose</code></td>
<td>``XMMatrixDecompose``</td>
</tr>
<tr>
<td><code>D3DXMatrixTranspose</code></td>
<td>``XMMatrixTranspose``</td>
</tr>
<tr>
<td><code>D3DXMatrixMultiply</code></td>
<td>``XMMatrixMultiply``</td>
</tr>
<tr>
<td><code>D3DXMatrixMultiplyTranspose</code></td>
<td>``XMMatrixMultiplyTranspose``</td>
</tr>
<tr>
<td><code>D3DXMatrixInverse</code></td>
<td>``XMMatrixInverse``</td>
</tr>
<tr>
<td><code>D3DXMatrixScaling</code></td>
<td>``XMMatrixScaling``</td>
</tr>
<tr>
<td><code>D3DXMatrixTranslation</code></td>
<td>``XMMatrixTranslation``</td>
</tr>
<tr>
<td><code>D3DXMatrixRotationX</code></td>
<td>``XMMatrixRotationX``</td>
</tr>
<tr>
<td><code>D3DXMatrixRotationY</code></td>
<td>``XMMatrixRotationY``</td>
</tr>
<tr>
<td><code>D3DXMatrixRotationZ</code></td>
<td>``XMMatrixRotationZ``</td>
</tr>
<tr>
<td><code>D3DXMatrixRotationAxis</code></td>
<td>``XMMatrixRotationAxis``</td>
</tr>
<tr>
<td><code>D3DXMatrixRotationQuaternion</code></td>
<td>``XMMatrixRotationQuaternion``</td>
</tr>
<tr>
<td><code>D3DXMatrixRotationYawPitchRoll</code></td>
<td>``XMMatrixRotationRollPitchYaw``
(Note the order of parameters is different:
D3DXMath takes yaw, pitch, roll,
DirectXMath takes pitch, yaw, roll)</td>
</tr>
<tr>
<td><code>D3DXMatrixTransformation</code></td>
<td>``XMMatrixTransformation``</td>
</tr>
<tr>
<td><code>D3DXMatrixTransformation2D</code></td>
<td>``XMMatrixTransformation2D``</td>
</tr>
<tr>
<td><code>D3DXMatrixAffineTransformation</code></td>
<td>``XMMatrixAffineTransformation``</td>
</tr>
<tr>
<td><code>D3DXMatrixAffineTransformation2D</code></td>
<td>``XMMatrixAffineTransformation2D``</td>
</tr>
<tr>
<td><code>D3DXMatrixLookAtRH</code></td>
<td>``XMMatrixLookAtRH``</td>
</tr>
<tr>
<td><code>D3DXMatrixLookAtLH</code></td>
<td>``XMMatrixLookAtLH``</td>
</tr>
<tr>
<td><code>D3DXMatrixPerspectiveRH</code></td>
<td>``XMMatrixPerspectiveRH``</td>
</tr>
<tr>
<td><code>D3DXMatrixPerspectiveLH</code></td>
<td>``XMMatrixPerspectiveLH``</td>
</tr>
<tr>
<td><code>D3DXMatrixPerspectiveFovRH</code></td>
<td>``XMMatrixPerspectiveFovRH``</td>
</tr>
<tr>
<td><code>D3DXMatrixPerspectiveFovLH</code></td>
<td>``XMMatrixPerspectiveFovLH``</td>
</tr>
<tr>
<td><code>D3DXMatrixPerspectiveOffCenterRH</code></td>
<td>``XMMatrixPerspectiveOffCenterRH``</td>
</tr>
<tr>
<td><code>D3DXMatrixPerspectiveOffCenterLH</code></td>
<td>``XMMatrixPerspectiveOffCenterLH``</td>
</tr>
<tr>
<td><code>D3DXMatrixOrthoRH</code></td>
<td>``XMMatrixOrthographicRH``</td>
</tr>
<tr>
<td><code>D3DXMatrixOrthoLH</code></td>
<td>``XMMatrixOrthographicLH``</td>
</tr>
<tr>
<td><code>D3DXMatrixOrthoOffCenterRH</code></td>
<td>``XMMatrixOrthographicOffCenterRH``</td>
</tr>
<tr>
<td><code>D3DXMatrixOrthoOffCenterLH</code></td>
<td>``XMMatrixOrthographicOffCenterLH``</td>
</tr>
<tr>
<td><code>D3DXMatrixShadow</code></td>
<td>``XMMatrixShadow``</td>
</tr>
<tr>
<td><code>D3DXMatrixReflect</code></td>
<td>``XMMatrixReflect``</td>
</tr>
<tr>
<td><code>D3DXQuaternionLength</code></td>
<td>``XMQuaternionLength``</td>
</tr>
<tr>
<td><code>D3DXQuaternionLengthSq</code></td>
<td>``XMQuaternionLengthSq``</td>
</tr>
<tr>
<td><code>D3DXQuaternionDot</code></td>
<td>``XMQuaternionDot``</td>
</tr>
<tr>
<td><code>D3DXQuaternionIdentity</code></td>
<td>``XMQuaternionIdentity``</td>
</tr>
<tr>
<td><code>D3DXQuaternionIsIdentity</code></td>
<td>``XMQuaternionIsIdentity``</td>
</tr>
<tr>
<td><code>D3DXQuaternionConjugate</code></td>
<td>``XMQuaternionConjugate``</td>
</tr>
<tr>
<td><code>D3DXQuaternionToAxisAngle</code></td>
<td>``XMQuaternionToAxisAngle``</td>
</tr>
<tr>
<td><code>D3DXQuaternionRotationMatrix</code></td>
<td>``XMQuaternionRotationMatrix``</td>
</tr>
<tr>
<td><code>D3DXQuaternionRotationAxis</code></td>
<td>``XMQuaternionRotationAxis``</td>
</tr>
<tr>
<td><code>D3DXQuaternionRotationYawPitchRoll</code></td>
<td>``XMQuaternionRotationRollPitchYaw``
(Note the order of parameters is different:
D3DXMath takes yaw, pitch, roll,
DirectXMath takes pitch, yaw, roll)</td>
</tr>
<tr>
<td><code>D3DXQuaternionMultiply</code></td>
<td>``XMQuaternionMultiply``</td>
</tr>
<tr>
<td><code>D3DXQuaternionNormalize</code></td>
<td>``XMQuaternionNormalizeEst``</td>
</tr>
<tr>
<td><code>D3DXQuaternionInverse</code></td>
<td>``XMQuaternionInverse``</td>
</tr>
<tr>
<td><code>D3DXQuaternionLn</code></td>
<td>``XMQuaternionLn``</td>
</tr>
<tr>
<td><code>D3DXQuaternionExp</code></td>
<td>``XMQuaternionExp``</td>
</tr>
<tr>
<td><code>D3DXQuaternionSlerp</code></td>
<td>``XMQuaternionSlerpV``</td>
</tr>
<tr>
<td><code>D3DXQuaternionSquad</code></td>
<td>``XMQuaternionSquadV``</td>
</tr>
<tr>
<td><code>D3DXQuaternionSquadSetup</code></td>
<td>``XMQuaternionSquadSetup``</td>
</tr>
<tr>
<td><code>D3DXQuaternionBaryCentric</code></td>
<td>``XMQuaternionBaryCentricV``</td>
</tr>
<tr>
<td><code>D3DXPlaneDot</code></td>
<td>``XMPlaneDot``</td>
</tr>
<tr>
<td><code>D3DXPlaneDotCoord</code></td>
<td>``XMPlaneDotCoord``</td>
</tr>
<tr>
<td><code>D3DXPlaneDotNormal</code></td>
<td>``XMPlaneDotNormal``</td>
</tr>
<tr>
<td><code>D3DXPlaneScale</code></td>
<td>``XMVectorScale``</td>
</tr>
<tr>
<td><code>D3DXPlaneNormalize</code></td>
<td>``XMPlaneNormalizeEst``</td>
</tr>
<tr>
<td><code>D3DXPlaneIntersectLine</code></td>
<td>``XMPlaneIntersectLine``</td>
</tr>
<tr>
<td><code>D3DXPlaneFromPointNormal</code></td>
<td>``XMPlaneFromPointNormal``</td>
</tr>
<tr>
<td><code>D3DXPlaneFromPoints</code></td>
<td>``XMPlaneFromPoints``</td>
</tr>
<tr>
<td><code>D3DXPlaneTransform</code></td>
<td>``XMPlaneTransform``</td>
</tr>
<tr>
<td><code>D3DXPlaneTransformArray</code></td>
<td>``XMPlaneTransformStream``</td>
</tr>
<tr>
<td><code>D3DXColorNegative</code></td>
<td>``XMColorNegative``</td>
</tr>
<tr>
<td><code>D3DXColorAdd</code></td>
<td>``XMVectorAdd``</td>
</tr>
<tr>
<td><code>D3DXColorSubtract</code></td>
<td>``XMVectorSubtract``</td>
</tr>
<tr>
<td><code>D3DXColorScale</code></td>
<td>``XMVectorScale``</td>
</tr>
<tr>
<td><code>D3DXColorModulate</code></td>
<td>``XMColorModulate``</td>
</tr>
<tr>
<td><code>D3DXColorLerp</code></td>
<td>``XMVectorLerpV``</td>
</tr>
<tr>
<td><code>D3DXColorAdjustSaturation</code></td>
<td>``XMColorAdjustSaturation``</td>
</tr>
<tr>
<td><code>D3DXColorAdjustContrast</code></td>
<td>``XMColorAdjustContrast``</td>
</tr>
<tr>
<td><code>D3DXFresnelTerm</code></td>
<td>``XMFresnelTerm``</td>
</tr>
</tbody>
</table>

<h1>Spherical Harmonics Math</h1>

The <a href="https://walbourn.github.io/spherical-harmonics-math/">SHmath</a> library is available as an add-on for DirectXMath.

<table border="1">
<tbody>
<tr>
<td><code>XMSHEvalDirection</code></td>
<td>Evaluates the Spherical Harmonic basis functions. Equivalent to <a href="https://docs.microsoft.com/en-us/windows/desktop/direct3d9/d3dxshevaldirection">D3DXSHEvalDirection</a> function.</td>
</tr>
<tr>
<td><code>XMSHRotate</code></td>
<td>Rotates SH vector by a rotation matrix. Equivalent to the <a href="https://docs.microsoft.com/en-us/windows/desktop/direct3d10/d3d10-d3dxshrotate>D3DXSHRotate</a> function.</td>
</tr>
<tr>
<td><code>XMSHRotateZ</code></td>
<td>Rotates the SH vector in the Z axis by an angle. Equivalent to the <a href="https://docs.microsoft.com/en-us/windows/desktop/direct3d9/d3dxshrotatez">D3DXSHRotateZ</a> function.</td>
</tr>
<tr>
<td><code>XMSHAdd</code></td>
<td>Adds two SH vectors. Equivalent to the <a href="https://docs.microsoft.com/en-us/windows/desktop/direct3d9/d3dxshadd>D3DXSHAdd</a> function.</td>
</tr>
<tr>
<td><code>XMSHScale</code></td>
<td>Scales a SH vector. Equivalent to the <a href="https://docs.microsoft.com/en-us/windows/desktop/direct3d10/d3d10-d3dxshscale">D3DXSHScale</a> function.</td>
</tr>
<tr>
<td><code>XMSHDot</code></td>
<td>Computes the dot product of two SH vectors. Equivalent to the <a href="https://docs.microsoft.com/en-us/windows/desktop/direct3d9/d3dxshdot">D3DXSHDot</a> function.</td>
</tr>
<tr>
<td><code>XMSHMultiply
XMSHMultiply2
XMSHMultiply3
XMSHMultiply4
XMSHMultiply5
XMSHMultiply6</code></td>
<td>Computes the product of two functions represented using SH. Equivalent to <a href="https://docs.microsoft.com/en-us/windows/desktop/direct3d9/d3dxshmultiply2">D3DXSHMultiply2</a>, ``D3DXSHMultiply3``, ``D3DXSHMultiply4``, ``D3DXSHMultiply5``, and ``D3DXSHMultiply6``.</td>
</tr>
<tr>
<td><code>XMSHEvalDirectionalLight</code></td>
<td>Evaluates a directional light and returns spectral SH data. Equivalent to the <a href="https://docs.microsoft.com/en-us/windows/desktop/direct3d10/d3d10-d3dxshevaldirectionallight">D3DXSHEvalDirectionalLight</a> function.</td>
</tr>
<tr>
<td><code>XMSHEvalSphericalLight</code></td>
<td>Evaluates a spherical light and returns spectral SH data. Equivalent to the <a href="https://docs.microsoft.com/en-us/windows/desktop/direct3d9/d3dxshevalsphericallight">D3DXEvalSphericalLight</a> function.</td>
</tr>
<tr>
<td><code>XMSHEvalConeLight</code></td>
<td>Evaluates a light that is a cone of constant intensity and returns spectral SH data. Equivalent to the <a href="https://docs.microsoft.com/en-us/windows/desktop/direct3d10/d3d10-d3dxshevalconelight">D3DXSHEvalConeLight</a> function.</td>
</tr>
<tr>
<td><code>XMSHEvalHemisphereLight</code></td>
<td>Evaluates a light that is a linear interpolant between two colors over the sphere. Equivalent to the <a href="https://docs.microsoft.com/en-us/windows/desktop/direct3d10/d3d10-d3dxshevalhemispherelight">D3DXSHEvalHemisphereLight</a> function.</td>
</tr>
<tr>
<td><code>SHProjectCubeMap</code></td>
<td>Projects a function represented in a cube map into spherical harmonics. Equivalent to the <a href="https://docs.microsoft.com/en-us/windows/desktop/direct3d11/d3dx11shprojectcubemap">D3DX11SHProjectCubeMap</a> function.</td>
</tr>
</tbody>
</table>

<h1>Effects (FX)</h1>

The <a href="https://walbourn.github.io/effects-for-direct3d-11-update/">Effects system for Direct3D 11</a> is primarily provided as a porting aid for older code. The library is available on <a href="http://go.microsoft.com/fwlink/p/?LinkId=271568">CodePlex</a> and <a href="https://github.com/Microsoft/FX11">GitHub</a>.

<table border="1">
<tbody>
<tr>
<td><code>D3DXCreateEffect
D3DXCreateEffectEx
D3DXCreateEffectFromResource
D3DXCreateEffectFromResourceEx
D3D10CompileEffectFromMemory</code></td>
<td><code>D3DX11CompileEffectFromMemory</code></td>
</tr>
<tr>
<td><code>D3DXCreateEffectFromFile
D3DXCreateEffectFromFileEx</code></td>
<td><code>D3DX11CompileEffectFromFile</code></td>
</tr>
<tr>
<td><code>D3D10CreateEffectFromMemory</code></td>
<td><code>D3DX11CreateEffectFromMemory</code></td>
</tr>
<tr>
<td><code>D3DXCreateEffectPool
D3D10CreateEffectPoolFromMemory</code></td>
<td>Effects 11 does not support 'effect pools'
or <code>D3DCOMPILE_EFFECT_CHILD_EFFECT</code>. Effect <a href="https://docs.microsoft.com/en-us/windows/desktop/direct3d11/d3d11-effect-group-syntax">groups</a> provide a more efficient solution for common scenarios previously addressed with 'effect pools'</td>
</tr>
<tr>
<td><code>D3DXDisassembleEffect
D3D10DisassembleEffect</code></td>
<td><code>D3DDisassemble</code>
<code>D3DDisassemble10Effect</code> in D3DCompile</td>
</tr>
</tbody>
</table>

<h1>Performance/Profiling</h1>
D3DX9 included the entry-points intercepted by PIX for Windows for performance profiling. This is now handled by the DirectX runtime directly and is monitored by the Visual Studio Graphics Diagnostics tool.
<table border="1">
<tbody>
<tr>
<td><code>D3DPERF_BeginEvent
D3DPERF_EndEvent
D3DPERF_SetMarker
D3DPERF_SetRegion
D3DPERF_QueryRepeatFrame
D3DPERF_SetOptions
D3DPERF_GetStatus</code></td>
<td><a href="https://docs.microsoft.com/en-us/windows/desktop/api/d3d11_1/nn-d3d11_1-id3duserdefinedannotation">ID3DUserDefinedAnnotation</a> which is supported by the DirectX 11.1 runtime on Windows 8.x and Windows 7 Service Pack 1 + <a href="https://walbourn.github.io/directx-11-1-and-windows-7-update/">KB2670838</a>

If using DirectX 11.2+, you can use <a href="https://docs.microsoft.com/en-us/windows/desktop/api/d3d11_2/nn-d3d11_2-id3d11devicecontext2">ID3D11DeviceContext2</a> directly for BeginEventInt, SetMarkerInt, and EndEvent.</td>
</tr>
</tbody>
</table>

<h1>UVAtlas Functions</h1>

The <a href="https://walbourn.github.io/uvatlas-return-of-the-isochart/">UVAtlas</a> library is intended for isochart creation offline with tools, although it can be used at runtime. You can find the library on <a href="http://go.microsoft.com/fwlink/?LinkID=512686">CodePlex</a> and <a href="https://github.com/Microsoft/UVAtlas">GitHub</a>.
<table border="1">
<tbody>
<tr>
<td><code>D3DXUVAtlasCreate</code></td>
<td><code>UVAtlasCreate</code></td>
</tr>
<tr>
<td><code>D3DXUVAtlasPartition</code></td>
<td><code>UVAtlasPartition</code></td>
</tr>
<tr>
<td><code>D3DXUVAtlasPack</code></td>
<td><code>UVAtlasPack</code></td>
</tr>
<tr>
<td><code>D3DXComputeIMTFromPerVertexSignal</code></td>
<td><code>UVAtlasComputeIMTFromPerVertexSignal</code></td>
</tr>
<tr>
<td><code>D3DXComputeIMTFromPerTexelSignal</code></td>
<td><code>UVAtlasComputeIMTFromPerTexelSignal</code></td>
</tr>
<tr>
<td><code>D3DXComputeIMTFromSignal </code></td>
<td><code>UVAtlasComputeIMTFromSignal</code></td>
</tr>
<tr>
<td><code>D3DXComputeIMTFromTexture </code></td>
<td><code>UVAtlasComputeIMTFromTexture</code></td>
</tr>
</tbody>
</table>

<h1>Note</h1>

Careful readers will note that there are a few omissions from this table.

<ul>
 	<li>The <code>ID3DXMatrixStack</code> from D3DXMath doesn't have a new version, but it's actually pretty easy to implement your own. If someone has a burning need for this, please comment below.</li>
 	<li>The Precomputed Radiance Transfer (PRT) simulator is only available in closed-source form in legacy D3DX9. There are a number of papers that cover this technology in the literature, and the most generally useful parts of this functionality are the <a href="https://walbourn.github.io/spherical-harmonics-math/">SHmath</a> functions which are available.</li>
</ul>

<h1>Background</h1>

A few folks have asked why the D3DX libraries were deprecated in the first place in the transition to the Windows 8.x SDK. D3DX has been a useful set of utility code for Direct3D applications since Direct3D 7, but has a presented a number of challenges over the years. The primary one is that as a 'closed-source' utility library; it was difficult to debug, resolve performance issues, or apply hot-fixes without taking a dependency on a newer release of the DirectX SDK. Second, with the security requirements that came with the adoption of the <a href="http://www.microsoft.com/security/sdl/default.aspx">Secure Development Lifecycle</a> (SDL) at Microsoft, the transition to a DLL rather than a static library created a redistribution requirement that<a href="https://walbourn.github.io/not-so-direct-setup/"> complicated setup</a> for many developers--note that static libraries have their own problems such as being specific to a particular release of the compiler including service pack level. D3DX9 itself became a bit bloated which resulted in a DLL larger than many EXEs that used it. Finally, the actual usable content of subsequent generations of D3DX left D3DX11 with not much in it. The need for useful utility code is still there, but it is now being solved with various 'shared-source' solutions rather than a monolithic closed-source DLL like D3DX.