CUDA Path Tracer
================

**University of Pennsylvania, CIS 565: GPU Programming and Architecture, Project 3**

* (TODO) YOUR NAME HERE
* Tested on: (TODO) Windows 22, i7-2222 @ 2.22GHz 22GB, GTX 222 222MB (Moore 2222 Lab)

### (TODO: Your README)

*DO NOT* leave the README to the last minute! It is a crucial part of the
project, and we will not be able to grade you without a good README.





```c++
Color3f rayColor(const Ray& r, const Hittable& world, int depth)
{
    HitRecord rec;
    // if we've exceeded the ray bounce limit, no more light is gathered
    if (depth <= 0)
    {
        return Color3f(0.0f, 0.0f, 0.0f);
    }

    // use 0.0001f to fix shadow acne
    if (world.hit(r, 0.001f, infinity, rec))
    {
        Ray scattered;
        Color3f attenuation;
        if (rec.mat->scatter(r, rec, attenuation, scattered))
        {
            return attenuation * rayColor(scattered, world, depth - 1);
        }
        return Color3f(0.0f, 0.0f, 0.0f);
    }

    Vector3f unitDirection = glm::normalize(r.direction());
    float t = 0.5f * (unitDirection[1] + 1.0f);
    return (1.0f - t) * Color3f(1.0f, 1.0f, 1.0f) 
        + t * Color3f(0.5f, 0.7f, 1.0f);
}

bool Metal::scatter(const Ray& rIn, const HitRecord& rec, 
    Color3f& attenuation, Ray& scattered) const
{
    Vector3f reflected = glm::reflect(glm::normalize(rIn.direction()), rec.normal);
    scattered = Ray(rec.p, reflected + m_fuzz * randomInUnitSphere());
    attenuation = m_albedo;
    // Why > 0.0f???
    return (glm::dot(scattered.direction(), rec.normal) > 0.0f);
}

```

if  (Metal) scatter returns false, return Color3f(0.f). This corresponds to 

```
        if (glm::dot(reflectedDir, normal) > 0.0f)
        {
			// scatter returns true
		}
        else {
			// scatter returns false
			// should set color to black
        }
```

Why white sphere boundary? 

Observe that sphere boundary has reflected ray direction goes below the surface. If we terminate the ray path (by setting the remaining bounces to 0), but don't set the color to black( color *= BLACK), there will be extra energy incorrectly residing in the ray path. 

TODO: Try to find the location of the pixel of the white boundary, and set break point to see the ray bounce. 

1) find out the location of the wrong pixel using GIMP
2) convert this coordinate into pixel index
3) set breakpoint based on pixel index to see how the color of this pixel is calculated based on rays.
