---
title: Molecular Graphics in PyMOL
permalink: /docs/molecular_graphics_pymol/
---

[WOBJ-spec]: http://www.fileformat.info/format/wavefrontobj/egff.htm
[PyMOL-main]: https://pymol.org/2/
[PyMOL-cgo]: https://pymolwiki.org/index.php/Category:CGO


CHAP writes a triangle mesh representation of the computed pore surface to a [Wavefront OBJ file][WOBJ-spec]. Unfortunately, [PyMOL][PyMOL-main] does not natively support the import of Wavefront OBJ meshes, which is why CHAP comes bundled with Python code that supports reading OBJ files and rendering them as [compiled graphics objects][PyMOL-cgo]. CHAP also provides a turnkey Python script for visualising the permeation pathway together with the channel protein.


## Loading and Displaying OBJ Files





