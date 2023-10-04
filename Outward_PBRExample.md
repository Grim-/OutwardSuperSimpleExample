I am attempting to make more of an effort to document how you do various things in Outward mostly because people starting modding for Outward this late will really struggle with absence of any documentation, because some things are not very intuitive even under the best circumstances.

This will assume you know atleast how to install and use SideLoader in order to export an item.


PBR - Physically based rendering and why it matters to you.


PBR is a mostly game dev technique for reducing the amount of memory used while still retaining good visuals/reaction to lightning/shadows, this is done by using one of each channel on an image (red, green, blue, alpha) and mapping those values to something in-game. 

Now you no longer need a texture for each thing you want to map (wetness, light, emission, metallic, smoothness) you can instead merge these into one texture, where each channel controls the value for a certain property while there is a certain 'standard' this is very much down to the implementation in the shader itself.


In the case of Outward this means

Red : Metallic 
Green : Unused as far as I can tell or maybe Ambient Occlusion
Blue : Smoothness
Alpha : Unused as far as I can tell 





Now I took the Wolf Tower Shield(A) and made two copies of the item (B Shiny Wolf Shield) and (C Super Shiny No Detail Wolf Shield), being sure to give them new IDs.

![Regular Shield at night near a torch](https://github.com/Grim-/OutwardSuperSimpleExample/assets/3288858/c484859d-8685-4674-abf3-7363eee09b6d)
![Edited Shield Smoothness and Metallic values increased](https://github.com/Grim-/OutwardSuperSimpleExample/assets/3288858/13a5a3c6-5d18-4f70-b2e0-852a1b2830c7)
![REgular Edited and Super shiny max smoothness shield together](https://github.com/Grim-/OutwardSuperSimpleExample/assets/3288858/27e6a88c-ed91-4254-a576-5164d8608d03)


Then I edited the _GenTex file which is the texture property used by Outwards default shader this is specifically our 'PBRMap'.

If a model does not have a _GenTex or it does but its white/black then you will need to create your own, 
















