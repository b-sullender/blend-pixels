# Blend Pixels

## About

This project addresses the challenge of accurately blending two pixels with alpha channels, resulting in a seamless and visually appealing composite image. The solution takes into account both the alpha channels of the pixels being blended and a user-provided alpha value that determines the final alpha channel of the blended pixel.

## Motivation

During the search for a solution to accurately blend pixels with alpha channels that results in a RGBA (4-channel) image, I decided to visualize the problem, do the math myself, and develop an algorithm that produces the correct result. The algorithm handles pixel blending and also manages alpha values, resulting in a smooth and controlled blending process.

LICENSE TERMS
=============
```
This software is provided 'as-is', without any express or implied
warranty.  In no event will the authors be held liable for any damages
arising from the use of this software.

Permission is granted to anyone to use this software for any purpose,
including commercial applications, and to alter it and redistribute it
freely, subject to the following restrictions:

(1) If any part of the source code or algorithm is used, it is requested that
    appropriate attribution is given to the authors by providing a link to
    the original repository or a reference to the authors name.
(2) Permission for use of this software is granted only if the user accepts
    full responsibility for any undesirable consequences; the authors accept
    NO LIABILITY for damages of any kind.

By using this software, you acknowledge that you have read and understood
these terms and agree to abide by them.
```

## Source Code

C Language:
```
// This code shows how to accurately blend pixels in C/C++
// Algorithm created by Brian Sullender in 2021

#include <stddef.h>
#include <math.h>

// Unsigned 8-bits
typedef unsigned char U8;

// Unsigned 32-bits
typedef unsigned int U32;

// This struct represents a single pixel
#pragma pack(push, 1)
struct PIXEL
{
    U8 Blue;
    U8 Green;
    U8 Red;
    U8 Alpha;
};
#pragma pack(pop)

// Blends two pixels based on a specified alpha value.
//   @param desPixel Pointer to the destination pixel.
//   @param pix2 The second pixel to blend with.
//   @param Alpha The alpha value for adjusting the result (0-255).
void BlendPixels(struct PIXEL* desPixel, U32 pix2, U8 Alpha)
{
    double b1, g1, r1, a1, b2, g2, r2, a2;
    double Alpha1, Alpha2, Remainder, T1, T2, T3, T4;

    // Convert input alpha to range 0.0 - 1.0
    Alpha1 = Alpha / 255.0;

    // Convert all the channels to have a range of 0.0 - 1.0
    b1 = desPixel->Blue / 255.0;
    g1 = desPixel->Green / 255.0;
    r1 = desPixel->Red / 255.0;
    a1 = desPixel->Alpha / 255.0;

    b2 = ((pix2 >> (8 * offsetof(struct PIXEL, Blue))) & 0xFF) / 255.0;
    g2 = ((pix2 >> (8 * offsetof(struct PIXEL, Green))) & 0xFF) / 255.0;
    r2 = ((pix2 >> (8 * offsetof(struct PIXEL, Red))) & 0xFF) / 255.0;
    a2 = ((pix2 >> (8 * offsetof(struct PIXEL, Alpha))) & 0xFF) / 255.0;

    // Get the percentage of the source alpha from the user-based alpha and get the remainder
    Alpha2 = Alpha1 / 1.0;
    Alpha2 *= a2;
    Remainder = 1.0 - Alpha2;

    // Save the results in T3 and T4
    T3 = Remainder;
    T4 = Alpha2;

    // Adjust the remainder from the destination alpha
    Remainder *= a1;
    Remainder /= 1.0;

    // Calculate the new remainder and adjust for the source channels
    Alpha2 += (T3 - Remainder);

    // Calculate the RGB channels
    T1 = r1 * Remainder;
    T2 = r2 * Alpha2;
    desPixel->Red = (U8)round(((T1 + T2) / 1.0) * 255.0);

    T1 = g1 * Remainder;
    T2 = g2 * Alpha2;
    desPixel->Green = (U8)round(((T1 + T2) / 1.0) * 255.0);

    T1 = b1 * Remainder;
    T2 = b2 * Alpha2;
    desPixel->Blue = (U8)round(((T1 + T2) / 1.0) * 255.0);

    // Calculate the new alpha channel
    desPixel->Alpha = (U8)round(((a1 + T4 * (1.0 - a1) / 1.0)) * 255.0);
}
```

## Contact Me

If you have questions you can contact me at [sullewarehouse@gmail.com](mailto:sullewarehouse@gmail.com)

