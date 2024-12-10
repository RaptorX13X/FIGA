# Furries In Galaxy Adventure

### About the project
F.I.G.A was a submission to April 2024 local gamejam completed in 28 hours of work. The topic was figa (or fig in english), we approached the topic by designing a game about a bat thats looking for figs in a cosmic environment while fighting off evil kobolds who prefer bananas.
The special mechanic made for this game is the echolocation, without which the world seems empty, but dangerous.

### How does it work
```csharp
public class Echolocation : MonoBehaviour
{
    public List<MeshRenderer> renderers = new List<MeshRenderer>();
    [SerializeField] private ParticleSystem particles;
    [SerializeField] private float cooldown;
    [SerializeField] private AudioClip echoSound;
    private float remainingCooldown;

    private void OnTriggerEnter(Collider other)
    {
        if(other.TryGetComponent(out MeshRenderer renderer))
        {
            renderers.Add(renderer);
        }
    }

    private void OnTriggerExit(Collider other)
    {
        if(other.TryGetComponent(out MeshRenderer renderer))
        {
            renderers.Remove(renderer);
        }
    }

    private void Start()
    {
        remainingCooldown = 0f;
    }


    private void Update()
    {
        if (remainingCooldown > 0)
        {
            remainingCooldown -= Time.deltaTime;
        }
        if (Input.GetKeyDown(KeyCode.Tab) && remainingCooldown <= 0f)
        {
            particles.Play();
            remainingCooldown = cooldown;
            SoundManager.instance.PlaySound(echoSound);
            foreach (MeshRenderer renderer in renderers)
            {
                if (renderer == null)
                {
                    continue;
                }
                renderer.enabled = true;
            }
        }
    }
}
```

Echolocation is necessary to progress through the game. At the beginning every game object, except for the starting space-island and player character, has its renderer turned off. Through echolocation players can uncover islands, pick ups and enemies. 
-Player character has a permanent trigger collider that collects all objects with a mesh renderer attached, and removes them when outside of colliders range.
-When players click the tab button, the player character lets out a burst of particles, a sound indicating the skill being used and enables all mesh renderers in range.
-When an object is destroyed it may stay on the list permanently. To avoid trying to enable a renderer that doesn't exist anymore the foreach loop skips objects that are marked as null.
-While scripts in this project may seem crude due to limited amount of time during the gamejam, in these circumstances what is important is that they work.
