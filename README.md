# OpenCV 2.4 with CUDA 9 Patch

Based on [this StackOverflow question](https://stackoverflow.com/questions/46584000/cmake-error-variables-are-set-to-notfound),
this repository contains a quick patch for compiling OpenCV 2.4 with CUDA 9.

To apply the patch, simply download OpenCV 2.4, and overwrite the files contained
in this repository. **Then set the `-DCUDA_GENERATION` variable reflecting
the available graphics card.** For example, compute capability 5.2 is
Maxwell generation, pr compute capability 7.0 is Volta generation (see code
snippet below for details).

Beyond the changes described in
[the StackOverflow question](https://stackoverflow.com/questions/46584000/cmake-error-variables-are-set-to-notfound),
the allowed CUDA generations in `OpenCVDetectCUDA.cmake` files need to be adapted:

    set(_generations "Fermi" "Kepler" "Maxwell" "Pascal" "Volta")

Additionally, these generations need to be reflected when determining
the CUDA architecture to use:

    set(__cuda_arch_ptx "")
    if(CUDA_GENERATION STREQUAL "Fermi")
      set(__cuda_arch_bin "2.0")
    elseif(CUDA_GENERATION STREQUAL "Kepler")
      set(__cuda_arch_bin "3.0 3.5 3.7")
    elseif(CUDA_GENERATION STREQUAL "Maxwell")
      set(__cuda_arch_bin "5.0 5.2")
    elseif(CUDA_GENERATION STREQUAL "Pascal")
      set(__cuda_arch_bin "6.0 6.1")
    elseif(CUDA_GENERATION STREQUAL "Volta")
      set(__cuda_arch_bin "7.0")
    elseif(CUDA_GENERATION STREQUAL "Auto")
      # ...
    endif()

Finally, the `--expt-relaxed-constexpr` flag needs to be set in `FindCUDA.cmake`:

    set(nvcc_flags "--expt-relaxed-constexpr")