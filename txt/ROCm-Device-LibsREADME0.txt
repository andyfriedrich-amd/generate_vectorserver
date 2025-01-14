<h2>OVERVIEW</h2>
<p>ROCm Device libraries.</p>
<p>This repository contains the sources and CMake build system for a
set of AMD specific device-side language runtime libraries.  Specifically:</p>
<p>| <strong>Name</strong> | <strong>Comments</strong> | <strong>Dependencies</strong> |
| --- | --- | --- |
| oclc<em> | Open Compute library controls (<a href="doc/OCML.md#controls">documentation</a>) | |
| ocml | Open Compute Math library (<a href="doc/OCML.md">documentation</a>) | oclc</em> |
| ockl | Open Compute Kernel library (<a href="doc/OCKL.md">documentation</a>) | oclc<em> |
| opencl | OpenCL built-in library | ocml, ockl, oclc</em> |
| hip | HIP built-in library | ocml, ockl, oclc<em> |
| hc | Heterogeneous Compute built-in library | ocml, ockl, oclc</em> |</p>
<p>Refer to <a href="LICENSE.TXT">LICENSE.TXT</a> for license information.</p>
<h2>BUILDING</h2>
<p>The build requires clang and several llvm development tools. This can
be built using the amd-stg-open branch of the RadeonOpenCompute
modified llvm-project repository, but the upstream llvm-project should
also work.</p>
<p>There are two different methods to build the device libraries; as a
standalone project or as an llvm external subproject.</p>
<p>For a standalone build, this will find a preexisting clang and llvm
tools using the standard cmake search mechanisms. If you wish to use a
specific build, you can specify this with the CMAKE_PREFIX_PATH
variable:</p>
<pre><code>git clone https://github.com/RadeonOpenCompute/ROCm-Device-Libs.git -b amd-stg-open
</code></pre>
<p>and from its top level run the following commands:</p>
<pre><code>mkdir -p build
cd build
export LLVM_BUILD=... (path to LLVM build directory created previously)
cmake -DCMAKE_PREFIX_PATH=$LLVM_BUILD ..
make
</code></pre>
<p>To build as an llvm external project:</p>
<pre><code>LLVM_PROJECT_ROOT=llvm-project-rocm
git clone https://github.com/RadeonOpenCompute/llvm-project.git -b amd-stg-open ${LLVM_PROJECT_ROOT}
cd ${LLVM_PROJECT_ROOT}
mkdir -p build
cd build

cmake ${LLVM_PROJECT_ROOT}/llvm -DCMAKE_BUILD_TYPE=Release \
      -DLLVM_ENABLE_PROJECTS="clang;lld" \
      -DLLVM_EXTERNAL_PROJECTS="device-libs" \
      -DLLVM_EXTERNAL_DEVICE_LIBS_SOURCE_DIR=/path/to/ROCm-Device-Libs
</code></pre>
<p>Testing requires the amdhsacod utility from ROCm Runtime.</p>
<p>To install artifacts:
    make install</p>
<p>To create packages for the library:
   make package</p>
<h2>USING BITCODE LIBRARIES</h2>
<p>The ROCm language compilers and runtimes automatically link the
required bitcode files invoked during the process of creating a code
object. clang will search for these libraries by default when
targeting amdhsa, in the default ROCm install location. To specify a
specific set of libraries, the --rocm-path argument can point to the
root directory where the bitcode libraries are installed, which is the
recommended way to link the libraries.</p>
<pre><code>$LLVM_BUILD/bin/clang -x cl -Xclang -finclude-default-header \
  -target amdgcn-amd-amdhsa -mcpu=gfx900 \
  --rocm-path=/srv/git/ROCm-Device-Libs/build/dist
</code></pre>
<p>These can be manually linked, but is generally not recommended. The
set of libraries linked should be in sync with the corresponding
compiler flags and target options. The default library linking can be
disabled with -nogpulib, and a manual linking invocation might look
like as follows:</p>
<pre><code>$LLVM_BUILD/bin/clang -x cl -Xclang -finclude-default-header \
    -nogpulib -target amdgcn-amd-amdhsa -mcpu=gfx900 \
    -Xclang -mlink-bitcode-file -Xclang /srv/git/ROCm-Device-Libs/build/dist/amdgcn/bitcode/opencl/opencl.bc \
    -Xclang -mlink-bitcode-file -Xclang /srv/git/ROCm-Device-Libs/build/dist/amdgcn/bitcode/ocml/ocml.bc \
    -Xclang -mlink-bitcode-file -Xclang /srv/git/ROCm-Device-Libs/build/dist/amdgcn/bitcode/ockl/ockl.bc \
    -Xclang -mlink-bitcode-file -Xclang /srv/git/ROCm-Device-Libs/build/dist/amdgcn/bitcode/oclc/oclc_correctly_rounded_sqrt_off.bc \
    -Xclang -mlink-bitcode-file -Xclang /srv/git/ROCm-Device-Libs/build/dist/amdgcn/bitcode/oclc/oclc_daz_opt_off.bc \
    -Xclang -mlink-bitcode-file -Xclang /srv/git/ROCm-Device-Libs/build/dist/amdgcn/bitcode/oclc/oclc_finite_only_off.bc \
    -Xclang -mlink-bitcode-file -Xclang /srv/git/ROCm-Device-Libs/build/dist/amdgcn/bitcode/oclc/oclc_unsafe_math_off.bc \
    -Xclang -mlink-bitcode-file -Xclang /srv/git/ROCm-Device-Libs/build/dist/amdgcn/bitcode/oclc/oclc_wavefrontsize64_on.bc \
    -Xclang -mlink-bitcode-file -Xclang /srv/git/ROCm-Device-Libs/build/dist/amdgcn/bitcode/oclc/oclc_isa_version_900.bc \
    test.cl -o test.so
</code></pre>
<h3>USING FROM CMAKE</h3>
<p>The bitcode libraries are exported as CMake targets, organized in a CMake
package. You can depend on this package using
<code>find_package(AMDDeviceLibs REQUIRED CONFIG)</code> after ensuring the
<code>CMAKE_PREFIX_PATH</code> includes either the build directory or install prefix of
the bitcode libraries. The package defines a variable
<code>AMD_DEVICE_LIBS_TARGETS</code> containing a list of the exported CMake
targets.</p>