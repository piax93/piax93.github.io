---
layout: post
title:  "Time to move around"
date:   2017-05-02
categories: dgi
---

As I said in the previous post, the natural next feature to add to this little piece of software is the ability for the user to move around in the scene. The first thing to do is to get input from keyboard and mouse and, fortunately, SDL2 provides a very simple interface for that:

```c++
int mousedx, mousedy;
Uint8* keystate = SDL_GetKeyboardState(NULL);
SDL_GetRelativeMouseState(&mousedx, &mousedy);
```

Now that we have this input information it is possible to translate it into transformations of the camera object. For translation the keys `WASD` were used and, depending on the key pressed, the camera center is translated alongside the `forward` vector or the cross product between the `forward` and the `up` vectors (which will end up pointing to the right of the view). The amount of movement is evaluated taking into account the time passed from the last frame and multiplying it for a fixed velocity value. 
A bit more work is necessary to calculate the camera rotation from the mouse input, but it is not too complicated overall. First, it is necessary to transform the mouse movement on the screen from pixels to radians. Then the `forward` vector is recalculated accordingly to spherical coordinates while the `up` vector can be left lying on the Y axis just modifying its orientation in case the view goes upside down.

```c++
// Eval rotation angle in radians
angle.x += glm::radians((dx / screenSize.x) * fov.x);
angle.y += glm::radians((dy / screenSize.y) * fov.y);
// Update forward and up vectors
forward.x = glm::cos(angle.y) * glm::sin(angle.x);
forward.y = -glm::sin(angle.y);
forward.z = -glm::cos(angle.x) * glm::cos(angle.y);
up.y = glm::cos(angle.y) < 0.0f ? -1.0f : 1.0f;
```

The result can be observed in the short video demonstration below.

<video width="640" height="360" controls>
  <source src="{{site.videos}}/camera_move.mp4" type="video/mp4">
</video>



Now everything is really ready and probably the next post will start discussing about cloth physics.