# Understanding ComfyUI seed

The Seed of the Ksampler is assigned to all the images generated in a batch size. Repeating the same Seed with the same Ksampler will produce the same 3 images. This reference Seed is the Seed from the first image. The Seed of the following images (2 and 3) is different. The x% variation applied to images 2 and 3 depends on the sampler. Most samplers inject noise for the images that create a variation of the first image. Euler is the exception. Euler gives the same 3 images. However, ComfyUI has added its own x% variation to images 2 and 3, which accentuates the change even more. Using Euler and native Ksampler, instead of having three identical images, we get 3 different images. This is why we can't have the Seed of the images that follow the first, because the sampler variation is random (probably each sampler has a mathematical formulas that determine the injection rate). 

Let see the result with different sampler 
![workflow-3](https://github.com/Creative-comfyUI/seed_eng/assets/166729777/ef6da483-226a-451c-b724-bd65aea00ddb)

With different Ksamplers we get different result for the same seed as explained before. Ksampler (inspire) and Ksampler variation... don't apply the ComfyUI variation which explains why we have the same 3 images. Ksampler fooocus apply an X% variation as comfyUI but it is not the same variation as the result is not totally the same.  In all cases except Fooocus we have the same first image. This means that the Fooocus Ksampler didn't use the same seed calculation and explains why image 2 and 3 are different from comfyUI's Ksampler.

This example was done with Euler. lets try with dpmpp_2m 
![workflow-4](https://github.com/Creative-comfyUI/seed_eng/assets/166729777/f9405db6-4634-45d4-92d1-6b67835749ea)

This time the 3 images are different for all Ksamplers except Ksampler Variation. Look carefully for Ksampler (inspire) the difference. Pictures 2 and 3 are slightly different, even the landscape in the bottle (the colours). Ksampler variation is the more coherent as it keeps the same variation and blocks the variation provided by the scheduler.  Ksampler inspire doesn't offer this possibility, as you can see the batch seed node is set to variation str inc :0.01, which means that it applies a slight variation. 

Moreover, the noise in comfyUI is calculated using the CPU, see pytorch reference, but this is not the case in A111 where it is calculated using the GPU. Ksampler offers the possibility to apply in comfyUI the GPU formulation which gives 3 different images for the same seed. The first image is different but the difference between the 3 images is much more important even with the same parameter for the batch seed node. Applying Euler instead of dpmpp gives 3 different image too. That mean the GPU calculation method add X% variation as comfyUI otherwise for Euler we should have the same 3 images. 

Calculation with GPU instead of CPU  A111 type dpmpp	 Calculation with GPU instead of CPU  A111 type Euler 
![Screenshot 2024-05-24 at 8 12 48 PM](https://github.com/Creative-comfyUI/seed_eng/assets/166729777/c7079006-aebf-4b49-8e57-9b4c40335a5c) 		![Screenshot 2024-05-24 at 8 18 18 PM](https://github.com/Creative-comfyUI/seed_eng/assets/166729777/30a1864b-0c38-495c-8fac-d3abbb53f5f3)

Ksampler Variation and Ksampler offer the ability to add X% variation to an with a fixed seed. This means that we have a seed for the image and a seed for the variation, giving us much more flexibility and control over our images. 

Lets apply with Euler scheduler a variation of 0.6 for the two ksampler with a fixed seed variation 
![Screenshot 2024-05-24 at 11 10 28 PM](https://github.com/Creative-comfyUI/seed_eng/assets/166729777/c0f770e7-0b13-4732-84b5-8ca827ad29aa)

As you can see, the Ksampler inspire uses the same variation for the 3 images, whereas the Ksampler variation seems to use the 0.6 variation in a more random way. The 3 images are different. For the same seed with the same variation strength, we don't have the same images. Ksampler inspire's variation strength is used in a more logical way. Using dpmpp 2 with ksampler inspire will give a very slight difference between the 3 images. 

The conclusion of this experience is that Ksampler needs to be changed to have a mix of Ksampler inspire and Ksampler variation and be the standard. 
From a designer's point of view a batch of 3 images should be slightly different not a completely different image. The designer should design the amount of variation and the type of variation not the one who programmed it. Ksampler needs to be revised to give the user more flexibility and much more control over the rendering of the image. 

I am still surprised that no one has yet answered my question as to how this variation is calculated and what the mathematical relationship is between the variation and the seed. Even this is usually calculated, I suppose, taking into account the seed. This means that we can have a seed for image variation 

I invite @cubiq, @ltdrdata and @gcomfyanonymous to comment
