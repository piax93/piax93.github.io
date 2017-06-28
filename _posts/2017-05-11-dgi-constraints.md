---
layout: post
title:  "Constraint system"
date:   2017-05-11
categories: dgi
---

In the previous post I presented the issue of the "explosive" nature of my mass-spring model when using strong springs. This had to be addressed somehow in order to build a stable simulation and the possible ways which I could come up with to solve the problem were basically three: give up the idea of an interactive demo and use small fixed time steps, change the integration method to an implicit one or change the physics model for the cloth. The first option did not look appealing since one of the premises of this small project was to have a real time demonstration and the second was not really employable as well due to the complexity which would have introduced.
As a consequence, looking for alternatives to a spring based system, I ended up reading about the possibility to use a simple fixed constraint system. This basically meant to substitute each spring with a constraint trying to keep the distance between the two particles attached at each end constant. The code portion applying _Hooke's law_ was then replaced with:

```c++
void Particle::offsetPosition(const glm::vec3& offset) {
    if (!fixed) v.position += offset;
}

...

void Spring::update() {
    // Non-elastic constraint
    glm::vec3 direction = b.v.position - a.v.position;
    glm::vec3 correction = direction * (1 - restLen / glm::length(direction));
    if (!(a.isFixed() || b.isFixed())) correction *= 0.5f;
    a.offsetPosition(correction);
    b.offsetPosition(-correction);
}
```

First the vector between the two particles is evaluated and it is then used to calculate the amount of correction needed to bring the particles back to the _rest_ distance (in case one of the particles is fixed, the correction factor for the other particle is doubled). Since these constraints are rigid and directly modify the position of the particle, in order to find a state satisfying all the ones defining the cloth object at the same time, it would be necessary to solve a very large linear system. Fortunately this can avoided by "relaxing" the system and this is achieved by applying the constraint rules iteratively:

```c++
for (int i = 0; i < ITERATIONS; i++) {
    for (auto it = springs.begin(); it != springs.end(); it++) {
        it->update();
    }
}
```

Obviously the number of iteration cannot be too large since it would significantly slow down the simulation, but, by manual testing, a value between 3 and 5 iteration was found to be sufficient to achieve a good looking result. The result is shown in the short video clip below, where the top-left corner of the cloth is fixed and the top-right corner is moved back and forth to demonstrate the flexibility of the mesh.

<video width="640" height="360" controls>
  <source src="{{site.videos}}/flag_moving.mp4" type="video/mp4">
</video>

Now that the cloth object is ready, it is possible to start thinking about implementing in it collision detection and response.
