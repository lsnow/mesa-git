## Mesa

Modified from https://github.com/Frogging-Family/mesa-git

https://gitlab.freedesktop.org/mesa/mesa

- Only radv(amd vulkan driver), zink and virgl are enabled.
- No LLVM.

```
git clone https://gitlab.freedesktop.org/mesa/mesa
git clone https://github.com/lsnow/mesa-git
cd mesa-git
# Edit PKGBUILD, change `mesa_dir`.
makepkg
```
