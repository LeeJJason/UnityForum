# Optimizing IL2CPP build times
使用IL2CPP构建项目时，项目构建时间会更长。但是，有几种方法可以大大减少构建时间：
* Use incremental building
  * 使用增量构建时，C ++编译器仅重新编译自上次构建以来已更改的文件。要使用增量构建，请将您的项目构建到先前的构建位置（而不删除目标目录）。
* Exclude project and target build folders from anti-malware software scans
  * 您可以在构建项目之前通过禁用反恶意软件来缩短构建时间。 （Unity Technologies的测试发现，在全新的Windows 10安装上禁用Windows Defender后，构建时间减少了50 – 66％。）
* Store your project and target build folder on a Solid State Drive (SSD)
  * 与传统硬盘驱动器（HDD）相比，固态驱动器（SSD）具有更快的读写速度。将IL代码转换为C ++并进行编译涉及大量的读/写操作。更快的存储设备可加快此过程。