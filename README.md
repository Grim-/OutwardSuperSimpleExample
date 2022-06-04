# OutwardSuperSimpleExample


Firstly I'd like to cover some Unity Basics.


Number 1 : 

__Everything you *see* inside Outward while playing is a GameObject, that tree? GameObject. Player? GameObject. Particle Effect System? GAMEOBJECT. __
So it is important to understand how these things work. 

A GameObject by itself is nothing (quite literally nothing but a positon in the World represented by the Transform component) by adding Mesh Renderers, Particle Systems, Trail Effects, all these things are what's called a component, a component is still a c# class but it extends from the MonoBehaviour class, this is a class *ALL* GameObjects(and their constituent components) in the scene have. 

It's the components on the GameObject that make it unique. (This is called Composition in programming jargon - an object is defined by the things that make it up)

Thanks to the magic of magic and Unity Explorer, I can show you quite exactly what that means.

In the picture below you can see two main panels, on the left the "Object Explorer" this allows to view everything in the current scene, as you can see in this case the current scene is "DontDestroyOnLoad" and I have selected the PlayerChar GameObject(everything in the scene is a what?) and expanded it. 

In the right panel you can see every component that is currently on the selected GameObject, there are a lot because the PlayerCharacter GameObject is very complex with many moving parts that are seemingly not connected at all, this isn't true ofcourse but it sure does look that way.


![UnityExplorer_PlayerGameObject_InScene](https://user-images.githubusercontent.com/3288858/172019801-93aa16fa-fb2a-47d3-ab7f-8de617ee3658.png)

(We'll come back to this later)

#YEAH BUT WHAT IS A COMPONENT?

Well ok but that doesn't really tell us still *what* a component is right?

Well a component is whatever you want it to be. Lets take a recent and thankfully very straightforward example where Avrixel wanted to create a simple mechanic for their class, if you want more information on the class itself please visit the #frost-warrior discord channel.

In terms of mechanics, they wanted to check that whenever the player dodges, if they have any stacks of their custom Status Effect named "Permafrost" and remove it.


So we need to create a MonoBehaviour that we add to the PlayerGameObject and it listens for when the player dodges and then removes a "stack" of the LeveledStatusEffect.


But how *exactly* do we 'listen' for when the player dodges? This is the part of modding where things get tricky, the straight forward answer is because someone already had a good idea how a game is set up, and had a rough idea where to look already. 

So then we use a tool like DNSpy - this is program used to decompile .dll files, this is extremely useful when modding as it allows you to see how the developers do things.


You can download DNSpy from https://github.com/dnSpy/dnSpy/releases. 

Once downloaded, run the dnSpy executable and click > File > Open 

![image](https://user-images.githubusercontent.com/3288858/172026117-644c6b2a-7158-4e99-9c1d-03d86102f9f0.png)

then navigate to where you have Outward installed once there navigate to Outward_Data/Managed and select Assembly-CSharp.dll.

![image](https://user-images.githubusercontent.com/3288858/172026171-044ed237-059f-436e-8b67-2af50fa29412.png)


This is the file Unity creates that contains I believe most if not all of the code we care about. 




Lets look at a screenshot of the Character class in DNSpy 







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

    //custom component to go on each Character (even AI)
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

