## Intro

I am attempting to make more of an effort to document how you do various things in Outward mostly because people starting modding for Outward this late will really struggle with absence of any documentation, because some things are not very intuitive even under the best circumstances.

This will assume you know atleast how to install and use SideLoader in order to export an item into a folder.

### PBR - Physically based rendering and why it matters to you.


## PBR 

is a mostly game dev technique (but not only) for reducing the amount of memory used on textures while still retaining good visuals/reaction to lightning/shadows, this is done by using one of each channel on an image (red, green, blue, alpha) and mapping those values to something in-game.

Now you no longer need a texture for each thing you want to map (wetness, light, emission, metallic, smoothness) you can instead merge these into one texture, where each channel controls the value for a certain property while there is a certain 'standard' this is very much down to the implementation in the shader itself.


## Outward
In the case of Outward this means

Red : Metallic 

Green : Unused as far as I can tell or maybe Ambient Occlusion

Blue : Smoothness

Alpha : Unused as far as I can tell 


### The Changes

Now I took the Wolf Tower Shield(A) and made two copies of the item (B Shiny Wolf Shield) and (C Super Shiny No Detail Wolf Shield) being sure to give them new IDs.
Then I edited the _GenTex file which is the texture property used by Outwards default shader this is specifically our 'PBRMap'.

If a model does not have a _GenTex or it does but its white/black then you will need to create your own, you can usually just draw a silouhette around the maintexture and draw the outline on top of it.

So if we understand PBR then we know, according to Outwards implementation if we want to make a particular object very shiny(blue) + metallic(red), then we need to color our _GenTex texture purple at the highest values.

Effectively the color we use determine how much each color channel is used.

We also have Shader properties we can play with you will find then in the folder for the material of that particular item in this case its ...'SideLoader/Test/Items/NewWolfShield/Textures/properties.xml' but it will vary slighly for you, in this file you have all the available shader properties where you can change and edit the values, in this case of the edited shield and the super shiny shield I used these values.



## The standard shield

Regular Shield at night

![Regular Shield at night near a torch](https://github.com/Grim-/OutwardSuperSimpleExample/assets/3288858/c484859d-8685-4674-abf3-7363eee09b6d)


Regular Gen Text

![_Original Gen Texture](https://github.com/Grim-/OutwardSuperSimpleExample/assets/3288858/c0bede67-67a1-4fd1-8208-0557aa579664)


## Edited Shield

Edited Shield Smoothness and Metallic values increased

![Edited Shield Smoothness and Metallic values increased](https://github.com/Grim-/OutwardSuperSimpleExample/assets/3288858/13a5a3c6-5d18-4f70-b2e0-852a1b2830c7)


Edited Gen Text

![_Increased MetallicSmoothness Texture](https://github.com/Grim-/OutwardSuperSimpleExample/assets/3288858/580c817a-dc6e-4eda-989b-aca32779c5d2)



Edited Wolf Shield
```xml
    <ShaderProperty xsi:type="FloatProp">
      <Name>_SmoothMin</Name>
      <Value>0.5</Value>
    </ShaderProperty>
    <ShaderProperty xsi:type="FloatProp">
      <Name>_SmoothMax</Name>
      <Value>1</Value>
    </ShaderProperty>
```


## Edited Maxed Smoothness and Metallic (Far right shield)
Edited and Super shiny max smoothness shield together (you might notice it looks almost like its wet because both smoothness and metallic are at the maximum possible setting generally you would never go this high
![Edited and Super shiny max smoothness shield together](https://github.com/Grim-/OutwardSuperSimpleExample/assets/3288858/27e6a88c-ed91-4254-a576-5164d8608d03)

Edited and Super shiny max smoothness shield GenTex
![Maximum MetallicSmoothness Texture](https://github.com/Grim-/OutwardSuperSimpleExample/assets/3288858/295fd402-63ea-4867-9714-5f3b1205e64a)

Edited Super Shiny Wolf Shield
```xml
    <ShaderProperty xsi:type="FloatProp">
      <Name>_SmoothMin</Name>
      <Value>0.9</Value>
    </ShaderProperty>
    <ShaderProperty xsi:type="FloatProp">
      <Name>_SmoothMax</Name>
      <Value>1</Value>
    </ShaderProperty>
```


Since this techinque also deals with specularity of an object too, you might want to change the _SpecColor property too, for example if you were making golden armour, you might set this to a very light orange/yellow

SpecColor
```xml
    <ShaderProperty xsi:type="ColorProp">
      <Name>_SpecColor</Name>
      <Value>
        <r>0.9</r>
        <g>0.6</g>
        <b>0</b>
        <a>1</a>
      </Value>
    </ShaderProperty>
```




For a more obvious example heres how this armour set looks before fiddling with the _GenTex

![Golden Sand'](https://github.com/Grim-/OutwardSuperSimpleExample/assets/3288858/a9fc5cbf-b6e6-47ff-bdec-ea38cfa850ac)


Heres that same armour but with metallic and smoothness values maxed and a golden specular color

Fully mapped smooth and metallic with no Ambient Occulsion (thats why its so dark)
![image](https://github.com/Grim-/OutwardSuperSimpleExample/assets/3288858/9a5a0599-7c7c-4a37-bb8c-bb455b26bfd4)


And here again the same armour but with proper mapping applied

Mapped and Colored
![image](https://github.com/Grim-/OutwardSuperSimpleExample/assets/3288858/11c60d44-8e14-49f3-8c08-7e72ec1c7c82)
MainTex
![image](https://github.com/Grim-/OutwardSuperSimpleExample/assets/3288858/f3538a46-97e7-4152-ae5f-dc0c73565009)
GenTex
![image](https://github.com/Grim-/OutwardSuperSimpleExample/assets/3288858/7245b3bc-fb56-41da-bc17-b61a770794d0)


As you can see I all the places I wanted to be both shiny and metallic, I painted purple, everywhere else such as the cloth is painted black.





