---
layout: post
title:  "Collision detection"
date:   2017-05-17
categories: dgi
---

I decided to implement collision detection and response for two different objects: a sphere and a cube.
For the sphere mesh, detecting collision is very simple: it is sufficient to check if the distance between a particle and the center of the sphere is less than the radius of the sphere. In case this test is positive, the particle is translated back to the point of the sphere defined as the intersection between its surface and the line on which lies the vector joining the center with the particle. It must be noted that in order to avoid glitching surface intersections in the rendering phase, the radius used to detect the collision is slightly larger than the actual radius of the sphere.

```c++
void Sphere::collide(Particle& p) const {
    glm::vec3 direction = p.v.position - center;
    if (glm::length(direction) < collision_radius) {
        p.resetPosition(glm::normalize(direction) * collision_radius + center);
    }
}
```

<video width="640" height="360" controls>
  <source src="{{site.videos}}/sphere_collision.mp4" type="video/mp4">
</video>

Moving to the cube mesh, the strategy employed could be considered as a custom application of the _slabs method_. It must be noted that this mesh is defined to always be aligned to the world axis so that an _AABB_ could be used. The collision is detected by evaluating the vector between the center of the cube and the particle and then checking if its components are shorter than the dimensions of the bounding box (just like in the previous case, the box size is slightly larger than the actual mesh). If the particle is found to be inside the cube, the previously evaluated vector is extended and then clipped to find its intersection with the bounding box which is then used as new position for the particle.

```c++
void Cube::collide(Particle& p) const {
    glm::vec3 ray = p.v.position - center;
    if (glm::abs(ray.x) < halfsideColl 
     && glm::abs(ray.y) < halfsideColl 
     && glm::abs(ray.z) < halfsideColl) {
        ray = glm::normalize(ray) * halfsideColl * 2.0f;
        if (glm::abs(ray.x) > halfsideColl && ray.x != 0.f)
            ray *= halfsideColl / glm::abs(ray.x);
        if (glm::abs(ray.y) > halfsideColl && ray.y != 0.f)
            ray *= halfsideColl / glm::abs(ray.y);
        if (glm::abs(ray.z) > halfsideColl && ray.z != 0.f)
            ray *= halfsideColl / glm::abs(ray.z);
        p.resetPosition(center + ray);
    }
}
```

(I hope you will forgive me, but I could not resist texturing the mesh as a _Companion Cube_)

<video width="640" height="360" controls>
  <source src="{{site.videos}}/cube_collision.mp4" type="video/mp4">
</video>