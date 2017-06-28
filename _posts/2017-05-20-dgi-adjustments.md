---
layout: post
title:  "Final adjustments"
date:   2017-05-20
categories: dgi
---

The final adjustments to this small project comprehended a refactoring of the folder structure and the addition of a small portion of code to recompute the normals of the vertices composing the cloth mesh at each time step. This was necessary to achieve a correct illumination of the mesh. The strategy employed consisted in resetting the normals to a zero-vector and then recomputing the one of each vertex as an average of the normals of the different triangles the vertex belongs to. The computation is done on each triangle using the cross product of two sides. This methods tries to create a smooth looking surface even though it is effectively composed by triangular sections.

```c++
// Reset normals
...
// Recompute normals
for (unsigned int i = 0; i < stripCount; i++) {
    for (unsigned int j = 0; j < hres - 1; j++) {
        index = i * hres + j;

        // Upper triangle of the quad
        norm = glm::cross(
            vertices[index + hres].position - vertices[index].position,
            vertices[index + 1].position - vertices[index].position
        );
        vertices[index].normal += norm;
        vertices[index + 1].normal += norm;
        vertices[index + hres].normal += norm;

        // Lower triangle of the quad
        norm = glm::cross(
            vertices[index + 1].position - vertices[index + hres + 1].position,
            vertices[index + hres].position - vertices[index + hres + 1].position
        );
        vertices[index + 1].normal += norm;
        vertices[index + hres].normal += norm;
        vertices[index + hres + 1].normal += norm;
    }
}
// Normalize normals
for (auto it = vertices.begin(); it != vertices.end(); it++)
    it->normal = glm::normalize(it->normal);
```

The result can be seen in the short video below in which the cloth assumes shaded or bright colors depending on the position of the particles relative to the light source.

<video width="640" height="360" controls>
  <source src="{{site.videos}}/cloth_normals.mp4" type="video/mp4">
</video>

The code of the whole project will be made public on [this repository](https://github.com/piax93/OpenGL_clothsim) as soon as the final grade for the course DH2323 is registered.