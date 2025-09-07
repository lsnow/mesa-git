## Mesa

[https://gitlab.freedesktop.org/mesa/mesa]

- Only radv(amd vulkan driver), zink and virgl are enabled.
- No LLVM.

```
git clone https://gitlab.freedesktop.org/mesa/mesa
git clone https://github.com/lsnow/mesa-git
# Modify PKGBUILD, change `mesa_dir`.
cd mesa-git
makepkg
```
