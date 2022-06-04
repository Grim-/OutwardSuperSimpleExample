# OutwardSuperSimpleExample


Firstly I'd like to cover some Unity Basics, one : Everything you *see* inside Outward while playing is a GameObject, that tree? GameObject. Player? GameObject. Particle Effect System? GAMEOBJECT.

So it is important to understand how these things work. 

A GameObject by itself is nothing (quite literally nothing but a positon in the World) by adding Mesh Renderers, Particle Systems, Trail Effects, all these things are what's called a component, a component is still a c# class but it extends from the MonoBehaviour class, this is a class *ALL* GameObjects in the scene have. 

Well ok but that doesn't really tell us still *what* a component is right? Well a component 


Some Unity Basics : 



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

