# Outward Modding - Taking the next step.

A written guide to help people of the [Outward Modding Discord](https://discord.gg/DPD9eGDU) getting started creating mods and understanding the Unity Engine. 
 

Firstly I'd like to cover some Unity Basics before moving on to the modding itself.

## Part One : Unity 101.

__Everything you *see* inside Outward while playing is a GameObject, that tree? GameObject. Player? GameObject. Particle Effect System? GAMEOBJECT__

__So it is important to understand how these things work.__

Lets start with a general set up in the Unity Editor first to better understand how everything fits together in Outward.

A Unity game is made up of *Scenes* - it is useful when starting out to think of 'Scenes' as 'levels' that contain GameObjects which themselves contain components. 

> This isn't exactly true as you may have a Scene that has GameObjects within it that are never seen by the player, or used as part of a level. Some games might only ever use 1 Scene and have everything dynamically loaded into that one scene in chunks as needed aka "chunking".


> Another case is the "DontDestroyOnLoad" scene which I will briefly cover later.


Here is a screenshot of a project from the *Unity Editor* on the right in the "Hierarchy" panel you can see the Scene Hierarchy "TestScene" which contains all the GameObjects present in this Scene.

The currently selected GameObject is "Bald Human" this is the furthest right panel and is called the "Inspector" usually the inspector starts with a GameObjects name and the transform component, you can also see a list of all the other components on this GameObject.

![image](https://user-images.githubusercontent.com/3288858/172053192-d617c139-f30a-4c56-9b94-09f72076ea54.png)


> If you look carefully above in the Unity Editor screenshot you can see that there is a second Scene further down the right hand "Hierarchy" panel there is another section called "DontDestroyOnLoad" this is a 'special' scene in Unity. 


Infact a good example of this is in Outward at any one time there can up be upto three *Scenes* running "DontDestroyOnLoad", "HideAndDontSave" and the Current Active Scene.

Thanks to the magic of magic and [Unity Explorer](https://outward.thunderstore.io/package/sinai-dev/UnityExplorer/), I can show you quite exactly what that means. 

![image](https://user-images.githubusercontent.com/3288858/172053933-a3d86b30-386b-43a0-8d1c-2e1c6f4d9373.png)

In the screenshot above you can see the interface for Unity Explorer an extremely useful mod developed by Sinai, which allows you to view the Scene Hierarchy in a somewhat similar way to the Unity Editor interface itself.

Take note of the "Scene" drop down and the names of those Scenes. "MainMenu_Empty" being the current scene and the one the player would "see" in-game.

You can see all kinds of GameObjects with all kinds of different components on them in the hierarchy, GameObjects are what the player sees in the Scene, including UI, videos, models anything.



## Part 1 A : GameObjects.

> Well ok a Scene is made up of GameObjects which is made up of Components, ok that means nothing to me.


Well, A GameObject by itself is nothing! Quite literally nothing but a positon in the World represented by the Transform component and is as simple as the GameObject gets.

It's the components on the GameObject that make it unique. Every GameObject will atleast have the Transform component, I will cover this more near the end.

*(This is called Composition in programming jargon - an object is defined by the things that make it up)*

You do not need to worry about this too much, if you follow along so far then you already have a basic understanding of Composition (GameObjects are composed of components which define the behaviour of the GameObject) but I did want to mention it briefly if you wish to learn more, The link below covers this in a lot more detail.

[Inheritance Versus Composition In Unity](http://www.sigtrapgames.com/unity-software-design-inheritance-and-composition/)

When we start adding Mesh Renderer components, Particle System components, Trail Effect components thats when the GameObject starts to become something meaningful, a component is still a C# class but it extends from the Unity MonoBehaviour class this is a class *ALL* GameObjects(and their constituent components) in the scene have. 


## Part 1 B : A Typical Scene in Outward.


> How does this relate to Outward again?

Outward is a game made in the Unity Engine. 

In the screenshot below you can see two main panels on the left the "Object Explorer" this allows us to view everything in the current scene, as you can see in this case the current scene is "DontDestroyOnLoad" and I have selected the PlayerChar GameObject.

(everything in the scene is a what? - A GAMEOBJECT) 

Now I have expanded the PlayerChar GameObject in the hierarchy to see its child GameObjects it is worth noting GameObjects can have child GameObjects with their own components and so on and this is what you can see under the PlayerChar_(UID) GameObject. 


In the right panel you can see every component that is currently on the selected GameObject there are a lot because the PlayerChar GameObject is very complex with many moving parts that are seemingly not connected at all, this isn't true of course but it sure does look that way.


![UnityExplorer_PlayerGameObject_InScene](https://user-images.githubusercontent.com/3288858/172019801-93aa16fa-fb2a-47d3-ab7f-8de617ee3658.png)

(We'll come back to this later)

Side note regarding "Special Scenes" like DontDestroyOnLoad

> Why does "DontDestroyOnLoad" keep popping up? 

DontDestroyOnLoad is a special scene created by the Unity engine itself used for containing GameObjects that you dont want to be destroyed when a scene exits.

In Outward for example it is much easier to ask Unity to add the Player GameObject to the "DontDestroyOnLoad" scene rather than saving all the changes made to the player > destroying the player > recreating the player in the new scene > then reapply the exact state the player was just in, every time you decide to change area.

So instead Unity offers a simple way to have GameObjects persist between scenes while keeping their state exactly as it was in the last scene, because technically it didnt change scenes at all it remained in the "DontDestroyOnLoad" scene.

## Part Two : YEAH BUT WHAT IS A COMPONENT?


Well ok a Scene contains GameObjects which are made up of components cool, but that doesn't really tell us still *what* a component is right?

Well a component is whatever you want it to be. At it's core just code. The MeshRenderer component which takes mesh data to draw the model mesh in-game, thats just a component. 

The Particle System that creates and manages thousands of particles, how they behave, how and what they can react to this is just another component.

The [MonoBehaviour](https://docs.unity3d.com/ScriptReference/MonoBehaviour.html) base component gives us access to a few methods that are called depending on the GameObjects 'Lifecycle'.

```c#
    //custom component
    public class YourComponentName : MonoBehaviour
    {        
        //This is called by the Unity Engine whenever a new component is created, its useful for things you only need to do one time only.
        public void Awake()
        {
            
        }
	
        //This is called by the Unity Engine on each component whenever the scene starts to play, this differs from Awake as an Object is only created once, but it may call start multiple times depending on how many times the scene it is contained in is started.
	public void Start()
	{
	
	}
	
        //This is called every frame by Unity (If you're curious how - Super Efficient Reflection) 
	public void Update()
	{
	
        }

        //NOTE 
        //There is very little chance you want to be using FixedUpdate with Outward Mods unless you are working with the Unity Physics system (PhysX) (Rigidbody components)
	//This is called every FIXED frame by Unity 
        //- This differs from Update in that by Default if your game runs at 60 FPS, update is called 60 times per second as you would expect, but FixedUpdate is strictly fixed and will be (by default settings) will be called 50 times for frame, meaning it can "skip" frames. This helps keeps physical reactions in Sync with Game play
	public void FixedUpdate()
	{
	
        }
    }
```


Armed with some basics lets move on to the current use-case.

Lets take a recent and thankfully very straightforward example where Avrixel wanted to create a mechanic for their class, if you want more information on the class itself please visit the #frost-warrior Outward Modding Discord channel.


In terms of mechanics Avrixel wanted to check that whenever the player dodges if they have any stacks of their custom Levelled Status Effect named "Permafrost" then remove a stack.

> A real world example of a LevelledStatusEffect would be the Alert buff "https://outward.fandom.com/wiki/Alert" as you can see from the Wikipedia page the more stacks  you have the more I-frames you have but you also take more damage when you fail to dodge.


> Here is also the link to how you can implement a LevelledStatusEffect on your own using XML and SideLoader, I encourage you to read the documents to see what SideLoader is capable of. (will update later!)
> https://sinai-dev.github.io/OSLDocs/#/API/SL_StatusEffect?id=sl_levelstatuseffect-sl_statuseffect

So we need to create a MonoBehaviour(Component) that we add to the Player *GameObject* and that listens for when the player dodges and then removes a "stack" of the LeveledStatusEffect.


> Ok but how *exactly* do we 'listen' for when the player dodges? 

This is the part of modding where things get tricky, it just experience and a lot of reading in DNSpy.

I like to stick to real world examples when I can, so in this particular case Faeryn of the modding discord has a fair amount of experience reading the code through DNSpy and similar programs, so they knew Avrixel would be best looking in the Character class at the SendDodgeTriggerTrivial method. 

So then we use a tool like DNSpy - this is a program used to decompile .dll files this is extremely useful when modding as it allows you to see how the developers do things in-game.


You can download [DNSpy - Here](https://github.com/dnSpy/dnSpy/releases) 

Once downloaded, run the dnSpy executable and click > File > Open 

![image](https://user-images.githubusercontent.com/3288858/172026117-644c6b2a-7158-4e99-9c1d-03d86102f9f0.png)

then navigate to where you have Outward installed once there navigate to Outward_Data/Managed and select Assembly-CSharp.dll.

![image](https://user-images.githubusercontent.com/3288858/172055463-f5cc953b-2343-44bc-ba94-1744626c0d54.png)

This is the file Unity creates that contains most, if not all of the code from the game. 


Lets look at a screenshot of the Character class in DNSpy by expanding Assembly-CSharp (the curly brackets denote a 'namespace' dont worry too much about this for now, just think of them as folders for code.)

Now to save time because there is a lot, a lot of code in the Assembly, I am going to skip straight to the SendDodgeTriggerTrivial method of the Character class. Almost everything you will want to read will exist inside the '{}' main Assembly namespace. 

![image](https://user-images.githubusercontent.com/3288858/172055270-dcb106d0-875f-4fac-89eb-e19d3fc88d25.png)


![image](https://user-images.githubusercontent.com/3288858/172026332-c20568b1-247f-46b3-9895-2e271bf841f4.png)

Specifically this function 

```c#
	[PunRPC]
	protected void SendDodgeTriggerTrivial(Vector3 _direction)
	{
		if (this.HasDodgeDirection)
		{
			int num = 0;
			if (this.DodgeRestricted)
			{
				num = 1;
			}
			else if (this.Inventory.SkillKnowledge.IsItemLearned(8205450))
			{
				num = 2;
			}
			this.m_animator.SetFloat("DodgeBlend", (float)num);
		}
		this.m_animator.SetTrigger("Dodge");
		if (this.m_currentlyChargingAttack)
		{
			this.SendCancelCharging();
		}
		this.m_dodgeSoundPlayer.Play(false);
		this.m_dodging = true;
		this.StopBlocking();
		if (this.OnDodgeEvent != null)
		{
			this.OnDodgeEvent();
		}
		if (this.m_characterSoundManager != null)
		{
			Global.AudioManager.PlaySoundAtPosition(this.m_characterSoundManager.GetDodgeSound(), base.transform, 0f, 1f, 1f, 1f, 1f);
		}
		base.SendMessage("DodgeTrigger", _direction, SendMessageOptions.DontRequireReceiver);
	}

```

If you are just starting out following this guide and you are reading this code thinking "ok, well this is about where I leave" - I would encourage you to remain seated! 


Its true there's no replacement for experience but there are also a lot of talented, helpful people on the Modding Discord with knowledge enough to at least point you in the correct direction!


The only line we are interested in is...

```c#     
  base.SendMessage("DodgeTrigger", _direction, SendMessageOptions.DontRequireReceiver);
```


This again requires some knowledge of how Unity works in this particular case but since I know the function *SendMessage* "SendMessage("DodgeTrigger", _direction, etc)" is used to call any function that matches the name **DodgeTrigger** that takes the a parameter called "_direction" on any other components that share the same GameObject parent.

So thats our in!

 We now know how to we can *listen* for when a player dodges by creating a MonoBehaviour that has a public method Called DodgeTrigger with a parameter called _direction and attaching it to our PlayerChar GameObject!


Here's the Unity Documents for [SendMessage](https://docs.unity3d.com/ScriptReference/Component.SendMessage.html) (as you can see its a method that any MonoBehaviour/Component can call)



Lets see what that would look like.

```c#
    //custom component
    public class DodgeListener : MonoBehaviour
    {
        private Character character;

        public void Awake()
        {
            character = GetComponent<Character>();
        }

        //Here's that public method created from the SendMessage Requirements. so now SendMessage in the SendDodgeTriggerTrivial method of the Character class will also call our Method as long as they share the same GameObject parent.
        public void DodgeTrigger(Vector3 _direction)
        {
           //Get the STatusEffectManager of the Current Character and Reduce the status level of the Permafrost Status Effect by 1.
            character.StatusEffectMngr.ReduceStatusLevel("Permafrost", 1);
        }
    }
```


private Character character;

> Whats this? This a private variable that we are going to set a *reference* for in the Awake method so we can access some things on this *instance* of the Character class.


```c#
	public void Awake()
	{
	    character = GetComponent<Character>();
	}
```

> What about this?

This is us actually setting that Character variable to a *reference of an instance* - if this doesn't make too much sense, 

Now we only need to get this reference once, doing it many times is costly, so the awake method is ideal for this as it is only ever called once, perfect.

Remember when we talked about everything being GameObjects in the scene and GameObjects are made up of components? 

Something you will do very often while working with any Unity game is using the GetComponent<T>() function to get a reference to a component where T is the type (the class) of component you wish to retrieve (aslong as it's base is MonoBehaviour!) This could be the Inventory (ItemContainer), the Status/BuffDebuffs (StatusEffectMngr), the one we want is the Character component. I'd encourage you to check out DNSpy and see all the methods and properties available.

```c#
	public void Awake()
	{
	    character = gameObject.GetComponent<Character>();
	}
```


gameObject.GetComponent<T> is the same thing for simplicity you don't need to use this (Its implied you want a Component off the current GameObject unless otherwise specified) but its a good reminder that again, we're just dealing with GameObjects and Components.





## Part 3 : BepInEx, Harmony and you.


So ok we know how to listen for a dodge, we've created a component that we can put on *any* GameObject with a Character component on it, how do we put that onto the Player actually in-game? 


Well thats where BepInEx and Harmony come in, If you set up a Visual Studio project from the Mod template linked earlier you will find you already have a class ready to build and put into the game but it does nothing. 


```c#

	[BepInPlugin(GUID, NAME, VERSION)]
	public class EmosModClass : BaseUnityPlugin
	{
		// Choose a GUID for your project. Change "myname" and "mymod".
		public const string GUID = "YOURUNIQUEID.YOURMODNAME";
		// Choose a NAME for your project, generally the same as your Assembly Name.
		public const string NAME = "Character Dodge Behaviour";
		// Increment the VERSION when you release a new version of your mod.
		public const string VERSION = "1.0.0";

		// For accessing your BepInEx Logger from outside of this class (MyMod.Log)
		internal static ManualLogSource Log;

		// Awake is called when your plugin is created. Use this to set up your mod.
		internal void Awake()
		{
		    Log = this.Logger;
		    //apply the patch
		    new Harmony(GUID).PatchAll();
		}
	}

```


We now need to introduce our code into the Outward code base this is infact very simple, when we click "Build" in Visual Studio and create a DLL from our code, any classes we make inside the project are compiled and included.


So our project now looks like this, we are almost there! 

```c#

        [BepInPlugin(GUID, NAME, VERSION)]
        public class EmosModClass : BaseUnityPlugin
        {
		// Choose a GUID for your project. Change "myname" and "mymod".
		public const string GUID = "YOURUNIQUEID.YOURMODNAME";
		// Choose a NAME for your project, generally the same as your Assembly Name.
		public const string NAME = "Character Dodge Behaviour";
		// Increment the VERSION when you release a new version of your mod.
		public const string VERSION = "1.0.0";

		// For accessing your BepInEx Logger from outside of this class (MyMod.Log)
		internal static ManualLogSource Log;

		// Awake is called when your plugin is created. Use this to set up your mod.
		internal void Awake()
		{
		    Log = this.Logger;
		    //apply the patch
		    new Harmony(GUID).PatchAll();
		}
    	}

	public class DodgeListener : MonoBehaviour
	{
	  private Character character;

	  public void Awake()
	  {
	    character = GetComponent<Character>();
	  }

	  public void DodgeTrigger(Vector3 _direction)
	  {
	    character.StatusEffectMngr.ReduceStatusLevel("Permafrost", 1);
	  }
	}

```


The final step is putting our freshly created component on the Player GameObject, there are a lot of methods to do this but Faeryn again showed a fairly simple way to ensure it's on every GameObject that has a Character component by creating a simple patch for the Character.Awake method (Remember the MonoBehaviour LifeCycle?)


Your first few times patching with Harmony this will seem *Anything* but simple, using Notations available through Harmony we are able to ask it to Patch the Awake method of ANY Character class instance, as you can see this simply adds our custom component to the current Character instance that has Awake called by Unity (again Awake is called once when a GameObject is created in scene.

```c#

        [HarmonyPatch(typeof(Character), nameof(Character.Awake))]
        public class Character_Awake
        {
            [HarmonyPrefix]
            static void Prefix(Character __instance)
            {
                  //on awake for whatever character this is add the component
                __instance.gameObject.AddComponent<DodgeListener>();
            }
        }
```

	

Now our Project file should look something like this in it's entirety.

```c#
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using UnityEngine;

namespace OutwardModTemplate
{
	[BepInPlugin(GUID, NAME, VERSION)]
	public class EmosModClass : BaseUnityPlugin
	{
		// Choose a GUID for your project. Change "myname" and "mymod".
		public const string GUID = "YOURUNIQUEID.YOURMODNAME";
		// Choose a NAME for your project, generally the same as your Assembly Name.
		public const string NAME = "Character Dodge Behaviour";
		// Increment the VERSION when you release a new version of your mod.
		public const string VERSION = "1.0.0";

		// For accessing your BepInEx Logger from outside of this class (MyMod.Log)
		internal static ManualLogSource Log;

		// Awake is called when your plugin is created. Use this to set up your mod.
		internal void Awake()
		{
			Log = this.Logger;
			//apply the patch
			new Harmony(GUID).PatchAll();
		}
	}


	//Our Custom Dodge Listner Component
	public class DodgeListener : MonoBehaviour
	{
		private Character character;

		public void Awake()
		{
			character = GetComponent<Character>();
		}

		public void DodgeTrigger(Vector3 _direction)
		{
			character.StatusEffectMngr.ReduceStatusLevel("Permafrost", 1);
		}
	}


	//the Harmony patch for the Character Awake method
	[HarmonyPatch(typeof(Character), nameof(Character.Awake))]
	public class Character_Awake
	{
		[HarmonyPrefix]
		static void Prefix(Character __instance)
		{
			//on awake for whatever character this is add the component
			__instance.gameObject.AddComponent<DodgeListener>();
		}
	}
}
```



Now if press Build > Build Project from Visual studio and go to output folder (If you're using the Mod Template its in the "Release" folder)

![image](https://user-images.githubusercontent.com/3288858/172028729-633ad607-3888-4de7-9b84-38128b0e58d1.png)

![image](https://user-images.githubusercontent.com/3288858/172028775-74d5dca3-6286-452c-b46a-52291816f120.png)


We now create a folder in our mod profile folder (If you are using R2ModMan) then Profile name > BepInEx > plugins  and create a new folder, this will be your mod folder, drop your created dll in here, now start the modded game (however you usually do that) 

![image](https://user-images.githubusercontent.com/3288858/172029533-b42a1943-f433-46a6-adc8-993d60929f21.png)


![image](https://user-images.githubusercontent.com/3288858/172028816-e0770c41-10fe-4b36-ae90-11b60ed1786e.png)


I have started a new game using our Mod file and opened Unity Explorer again and expanded the Player GameObject, as you can see the Player now successfully has the our component attached to it!

![image](https://user-images.githubusercontent.com/3288858/172029000-3feeb33d-e581-43a3-a011-e8cf4023a7f5.png)


You probably dont have Avrixels custom status effect so the code currently will do nothing for you, but if change our DodgeListener component, we can do something else instead!

## Part 4: Extra Credit


Lets change that shall we? And quickly touch on the Transform component and positions in Unity. 

Firstly it's important to Understand every GameObject has at the bare minimum a Transform component, the Transform components only job is to hold the information about the position, rotation and scale of the GameObject, nothing else. 

If you can get the transform of something you can just as easily get the GameObject itself where you find one, you will ALWAYS find the other and from that you can check if any component you want exists on the GameObject, some objects might not have them, some components might not be where you'd logically expect, thats ok, just ask in the discord, collectively there is a large base of knowledge between users they are happy to share.

So if we have a reference to the Players Scene GameObject, that means we can get the transform component fairly easily. 

Side note : 

A feature of c# are "properties" these like variables can be almost anything, a reference to a component, a function, or a simple variable. 

A GameObject always has a transform property 'gameObject.transform'. and a Transform always a gameObject property 'transform.gameObject'  you will see many properties of the Character class in DNSpy, you can see many properties of the Character class denoted with a wrench symbol, such as the players Inventory, but in our case case, we simply want the transform component.

![image](https://user-images.githubusercontent.com/3288858/172031266-d2a30757-537a-42c8-bc2b-1c5e77244a50.png)


We can use this to retrieve the players current position and the current input direction of our DodgeTrigger method in order to teleport the player in direction of our input.

This is not a good idea for a mod, collisions make up a large part of this game's navigation, open world stuff and you can easily find yourself falling through the world or any number of other things, but it is very visual, so lets crack on!


```c#
	//Our Custom Dodge Listner Component
	public class DodgeListener : MonoBehaviour
	{
		private Character character;

		public void Awake()
		{
			character = GetComponent<Character>();
		}

		public void DodgeTrigger(Vector3 _direction)
		{
			//_direction is a Vector3 variable type - This is a type common in Unity used for defining things that have 3 axis (x, y, z)
			//When the player dodges the direction they were pressing at the time of the dodge is passed along too, using this direction and our players CURRENT position we can make the player effecticely teleport in the direction we roll.
			//am I going to write this out the long way so you can see every step

			//remember how I said every GameObject exists in scene and without other components it only has a position in world space?
			//character.transform is a short cut to retrieve the Transform component of the current GameObject we can then access the .position value to find where the player is stood in world co-ordinates right now.
			//so we add the input _direct + the player positiion this gives up the place we want to teleport to relative to the players current position
			//lets also add a length variable so we can adjust how far we teleport in the direction of the input

			float TeleportLength = 10f;
	               //direction times Teleport Length + players position
			Vector3 TeleportDirection = (_direction * TeleportLength) + character.transform.position;

			//now we call the Outward Teleport function, passing in the new positon we calculated and the our characters current rotation (this is something else the Transform component handles)
			character.Teleport(TeleportDirection, character.transform.rotation);
		}
	}


```

If you update the DodgeTrigger code to the above, you will see that as you expect, once we start the game and press dodge, we teleport 10 units ahead of the input direction. 

I wrote this mini guide in the hopes of inspiring some budding modders take the first steps with a practical example. 

I hope this helps some what connect the dots of modding in Outward.

https://i.imgur.com/6OzRVwm.mp4


I plan to follow this up with a practical guide on creating *SIMPLE* Particle Effects in Unity for use in Outward. Please let me know via Discord about any issues, mistakes or other stuff, I go by 'Emo' in the modding discord.

Special Thanks, in no particular order

Sinai - of the Modding discord and Creator/Maintainer of SideLoader - without which ALL Outward modding, would be magnitudes more difficult, the impact SideLoader has on the Outward Modding community cannot be understated. 

I myself am nothing but a really enthusiastic amateur, I have been working on my own single player RPG for three years now and I have been working in Unity for a lot longer than that I tell you this to give you some context, that the sheer of amount of effort and love that has gone into SideLoader is honestly *staggering*.

IggyTheMad - The Glue of the discord, keeps things running, keeps people ticking, helps out everywhere, has a bajillion mods. Great guy!

Faeryn - Faeryn is another modder who makes a point to help out others on the modding discord and was what prompted this article originally, I have mostly shamelessly stolen Faeryns code and I am Lawyering up.

Avrixel - Who was subject 0 and the original target of the impromptu guide and Mod Developer of the in-progress Frost Warrior Skill Tree.

All the Jims that died while I was getting screenshots.
