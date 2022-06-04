# OutwardSuperSimpleExample


Firstly I'd like to cover some Unity Basics.


> Part One : Unity 101.

__Everything you *see* inside Outward while playing is a GameObject, that tree? GameObject. Player? GameObject. Particle Effect System? GAMEOBJECT. __
So it is important to understand how these things work. 

A GameObject by itself is nothing (quite literally nothing but a positon in the World represented by the Transform component) by adding Mesh Renderers, Particle Systems, Trail Effects, all these things are what's called a component, a component is still a c# class but it extends from the MonoBehaviour class, this is a class *ALL* GameObjects(and their constituent components) in the scene have. 

It's the components on the GameObject that make it unique. (This is called Composition in programming jargon - an object is defined by the things that make it up)

Thanks to the magic of magic and Unity Explorer, I can show you quite exactly what that means.

In the picture below you can see two main panels, on the left the "Object Explorer" this allows us to view everything in the current scene, as you can see in this case the current scene is "DontDestroyOnLoad" and I have selected the PlayerChar GameObject(everything in the scene is a what? a GAMEOBJECT) and expanded it, it's worth noting GameObjects can have child GameObjects with their own components on and this is what you can see under the PlayerChar GameObject. 

In the right panel you can see every component that is currently on the selected GameObject, there are a lot because the PlayerCharacter GameObject is very complex with many moving parts that are seemingly not connected at all, this isn't true ofcourse but it sure does look that way.


![UnityExplorer_PlayerGameObject_InScene](https://user-images.githubusercontent.com/3288858/172019801-93aa16fa-fb2a-47d3-ab7f-8de617ee3658.png)

(We'll come back to this later)


> Part Two : #YEAH BUT WHAT IS A COMPONENT?

Well ok but that doesn't really tell us still *what* a component is right?

Well a component is whatever you want it to be. At it's core just code.

The MonoBehaviour component gives us access to a few methods that are called depending on the GameObjects 'Lifecycle'

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


Visit : https://docs.unity3d.com/ScriptReference/MonoBehaviour.html For a Full list of the Lifecycle and methods available.

Armed with some basics lets move on to the current use-case.

Lets take a recent and thankfully very straightforward example where Avrixel wanted to create a mechanic for their class, if you want more information on the class itself please visit the #frost-warrior discord channel.


In terms of mechanics Avrixel wanted to check that whenever the player dodges if they have any stacks of their custom Levelled Status Effect named "Permafrost" and remove a stack.

> A real world example of a LevelledStatusEffect would be the Alert buff "https://outward.fandom.com/wiki/Alert" as you can see from the Wikipedia page the more stacks  you have the more Iframes you have but you also take more damage.
> Here is also the link to how you can implement a LevelledStatusEffect on your own using XML and SideLoader, I encourage you to read the documents to see what SideLoader is capable of.
> https://sinai-dev.github.io/OSLDocs/#/API/SL_StatusEffect?id=sl_levelstatuseffect-sl_statuseffect

So we need to create a MonoBehaviour that we add to the PlayerGameObject and that listens for when the player dodges and then removes a "stack" of the LeveledStatusEffect.







> But how *exactly* do we 'listen' for when the player dodges? 

This is the part of modding where things get tricky, its experience and a lot of reading in DNSpy.

I like to stick to real world examples when I can, so in this particular case Faeryn of the modding discord has a fair amount of experience reading the code through DNSpy and similar programs, so they knew Avrixel would be best looking in the Character class at the SendDodgeTriggerTrivial method. 

So then we use a tool like DNSpy - this is program used to decompile .dll files, this is extremely useful when modding as it allows you to see how the developers do things.


You can download DNSpy from https://github.com/dnSpy/dnSpy/releases. 

Once downloaded, run the dnSpy executable and click > File > Open 

![image](https://user-images.githubusercontent.com/3288858/172026117-644c6b2a-7158-4e99-9c1d-03d86102f9f0.png)

then navigate to where you have Outward installed once there navigate to Outward_Data/Managed and select Assembly-CSharp.dll.

![image](https://user-images.githubusercontent.com/3288858/172026171-044ed237-059f-436e-8b67-2af50fa29412.png)

This is the file Unity creates that contains most, if not all of the code from the game. 



Lets look at a screenshot of the Character class in DNSpy by expanding Assembly-CSharp (the curly brackets denote a 'namespace' dont worry too much about this for now, just think of them as folders for code.)

Now to save time because there is a lot, a lot of code in the Assembly, I am going to skip straight to the SendTrivialDodge method of the Character class. Almost everything you will want to read will exist inside the '{}' main Assembly namespace. 

![image](https://user-images.githubusercontent.com/3288858/172026332-c20568b1-247f-46b3-9895-2e271bf841f4.png)

Specifically this function 

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


If you are just starting out following this guide and you are reading this code thinking "ok, well this is about where I leave" - I would encourage you to remain seated! 

Its true there's no replacement for experience but there are also a lot of talented, helpful people on the Modding Discord with knowledge enough to at least point you in the correct direction!


The only line we are interested in is

```c#     
  base.SendMessage("DodgeTrigger", _direction, SendMessageOptions.DontRequireReceiver);
```


This again requires some knowledge of how Unity works but I know the function *SendMessage* 'SendMessage("DodgeTrigger, _direction, etc)' is used to call any function that matches the name **DodgeTrigger** that takes the a parameter called "_direction" on other components ** on the same GameObject **  

So thats our in!

 We now know how to we can *listen* for when a player dodges by creating a MonoBehaviour that has a public method Called DodgeTrigger with a parameter called _direction and attaching it to our PlayerChar GameObject!



Lets see what that would look like.

    //custom component to go on each
    public class DodgeListener : MonoBehaviour
    {
        //Whats this? 
        //We need a 'reference' to the Character component thats on the Same GameObject as our DodgeTrigger Script so we create a private variable
        private Character character;

        //now we only need to get this reference once, doing it many times is costly, so the awake method is perfect for this, only ever called once, perfect.
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



private Character character;

> Whats this? This a private variable that we are going to set in the Awake method so we can access some things on this instance of the Character class.


//now we only need to get this reference once, doing it many times is costly, so the awake method is perfect for this, only ever called once, perfect.
public void Awake()
{
    character = GetComponent<Character>();
}

> What about this?

Remember when we talked about everything being components? 

Something you will do very often while working with any Unity game is using the GetComponent function to get a reference to a component
GetComponent<T> where T is the type (the class) of component you wish to retrieve
gameObject.GetComponent<Character> is the same thing, for simplicity you don't need to use this, but its a good reminder that again, we're just dealing with GameObjects and Components.





        [BepInPlugin(GUID, NAME, VERSION)]
        public class EmosUniques_SeekingStone : BaseUnityPlugin
        {
        // Choose a GUID for your project. Change "myname" and "mymod".
        public const string GUID = "YOURUNIQUEID.YOURMODNAME";
        // Choose a NAME for your project, generally the same as your Assembly Name.
        public const string NAME = "Character Behaviour";
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

         // Ask Harmony to patch the Awake method of the Character class
         // 
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



