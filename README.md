# OutwardSuperSimpleExample


Firstly I'd like to cover some Unity Basics.


Number 1 : 

__Everything you *see* inside Outward while playing is a GameObject, that tree? GameObject. Player? GameObject. Particle Effect System? GAMEOBJECT. __
So it is important to understand how these things work. 

It's the components on the GameObject that make it unique. 

Thanks to the magic of magic and Unity Explorer, I can show you quite exactly what that means.

In the picture below you can see two main panels, on the left the "Object Explorer" this allows to view everything in the current scene, as you can see in this case the current scene is "DontDestroyOnLoad" and I have selected the PlayerChar GameObject(everything in the scene is a what?) and expanded it. 

In the right panel you can see every component that is currently on the selected GameObject, there are a lot because the PlayerCharacter GameObject is very complex with many moving parts that are seemingly not connected at all, this isn't true ofcourse but it sure does look that way.


![UnityExplorer_PlayerGameObject_InScene](https://user-images.githubusercontent.com/3288858/172019801-93aa16fa-fb2a-47d3-ab7f-8de617ee3658.png)




A GameObject by itself is nothing (quite literally nothing but a positon in the World) by adding Mesh Renderers, Particle Systems, Trail Effects, all these things are what's called a component, a component is still a c# class but it extends from the MonoBehaviour class, this is a class *ALL* GameObjects in the scene have. 

Well ok but that doesn't really tell us still *what* a component is right? Well a component is whatever you want it to be. Lets take a recent and thankfully very straightforward example where Avrixel wanted to create a simple mechanic for their class, if you want more information on the class itself please visit the #frost-warrior discord channel.

In terms of mechanics, they wanted to check that whenever the player dodges, if they have any stacks of their custom Status Effect named "Permafrost" and remove it.







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

