# OutwardSuperSimpleExample

namespace OutwardModTemplate
{
    [BepInPlugin(GUID, NAME, VERSION)]
    public class EmosUniques_SeekingStone : BaseUnityPlugin
    {
        // Choose a GUID for your project. Change "myname" and "mymod".
        public const string GUID = "EmoUniques.seekingstone";
        // Choose a NAME for your project, generally the same as your Assembly Name.
        public const string NAME = "Emo's Uniques - Seeking Stone";
        // Increment the VERSION when you release a new version of your mod.
        public const string VERSION = "1.0.0";

        // For accessing your BepInEx Logger from outside of this class (MyMod.Log)
        internal static ManualLogSource Log;

        public const string EXTRA_ACTION_KEY_MOD = "Emo_ExtraAction_Toggle_Modifier";
        public const string EXTRA_ACTION_KEY = "Emo_ExtraAction_Key";

        // Awake is called when your plugin is created. Use this to set up your mod.
        internal void Awake()
        {
            Log = this.Logger;
            //apply the patch
            new Harmony(GUID).PatchAll();
        }



         //Fearyns super mega patch
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
}
