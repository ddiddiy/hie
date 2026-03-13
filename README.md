# Unity

## Prac 2
### Translation - Up and Down
```csharp
using UnityEngine;
using UnityEngine.InputSystem;

public class Prac1_1 : MonoBehaviour
{
    private float speed = 1f;
    private Rigidbody2D rb;
    void Start()
    {
        rb = GetComponent<Rigidbody2D>();
    }

    void Update()
    {
        float moveX = Input.GetAxis("Horizontal");
        float moveY = Input.GetAxis("Vertical");
        Vector2 movement = new Vector2(moveX, moveY) ;
        rb.linearVelocity = movement * speed;
    }
}
```

### Rotation - Left and Right
```csharp
using UnityEngine;
using UnityEngine.InputSystem;

public class Prac1_2 : MonoBehaviour
{
    private float speed = 10f;
    private Rigidbody2D rb;
    void Start()
    {
        rb = GetComponent<Rigidbody2D>();
    }

    void Update()
    {
        float rotateX = Input.GetAxis("Horizontal");
        rb.angularVelocity = rotateX * speed;
    }
}
```

### Scale 
```csharp
using UnityEngine;

public class Prac1_3 : MonoBehaviour
{
    public float speed = 10f;
    private Rigidbody2D rb;
    void Start()
    {
        rb = GetComponent<Rigidbody2D>();
    }

    void Update()
    {
        float change = Input.GetAxis("Vertical");
        transform.localScale += Vector3.one * change * speed * Time.deltaTime;
    }
}
```

## Prac 3
### Jump
```csharp
using UnityEngine;

public class Prac2_1 : MonoBehaviour
{
    public float speed = 0.1f;
    private Rigidbody2D rb;
    void Start()
    {
        rb = GetComponent<Rigidbody2D>();
    }

    void Update()
    {
        if(Input.GetKeyDown(KeyCode.Space))
        {
            rb.linearVelocity = new Vector2(rb.linearVelocity.x, speed);
        }
        
    }
}
```

### Jump and Move
```csharp
using UnityEngine;

public class Prac2_2 : MonoBehaviour
{
    public float speed = 1f;
    private Rigidbody2D rb;
    void Start()
    {
        rb = GetComponent<Rigidbody2D>();
    }

    void Update()
    {
        float move = Input.GetAxisRaw("Horizontal");
        rb.linearVelocity = new Vector2(move * speed, rb.linearVelocity.y);

        if (Input.GetKeyDown(KeyCode.Space))
        {
            rb.linearVelocity = new Vector2(rb.linearVelocity.x, speed);
        }
    }
}
```

### Jump and Move and Color
```csharp
using UnityEngine;

public class Prac2_3 : MonoBehaviour
{
    public float move = 5f;
    private float jump = 5f;
    public Color left = Color.red;
    public Color right = Color.blue;
    public Color idle = Color.white;
    private SpriteRenderer sr;
    private Rigidbody2D rb;
    void Start()
    {
        rb = GetComponent<Rigidbody2D>();
        sr = GetComponent<SpriteRenderer>();
    }

    void Update()
    {
        float move = Input.GetAxisRaw("Horizontal");
        rb.linearVelocity = new Vector2(move * this.move, rb.linearVelocity.y);

        if (move < 0)
        {
            sr.color = left;
        }
        else if (move > 0)
        {
            sr.color = right;
        }
        else
        {
            sr.color = idle;
        }
    }
}
```

### Surface Effector and Jump
```csharp
using UnityEngine;

public class Prac2_4 : MonoBehaviour
{
    public float speed = 5f;
    public float jump = 5f;
    public Color left = Color.red;
    public Color right = Color.blue;
    public Color idle = Color.white;
    public Rigidbody2D rb;
    private SpriteRenderer sr;

    void Start()
    {
        rb = GetComponent<Rigidbody2D>();
        sr = GetComponent<SpriteRenderer>();
    }

    void Update()
    {
        float move = Input.GetAxisRaw("Horizontal");
        rb.linearVelocity = new Vector2(move * speed, rb.linearVelocity.y);

        if (move < 0)
        {
            sr.color = left;
        }
        else if (move > 0)
        {
            sr.color = right;
        }
        else
        {
            sr.color = idle;
        }   

        if (Input.GetKeyDown(KeyCode.Space))
        {
            rb.AddForce(Vector2.up * jump, ForceMode2D.Impulse);
        }
    }
}
```

## Prac 4
### Button
- create a canvas 
- create a button in the canvas
- update the text of the button to "Click Me"
- make a new script 
```csharp
using UnityEngine;

public class Prac4_1 : MonoBehaviour
{
    public void ClickButton()
    {
        Debug.Log("Button Clicked");
    }
}
```
- now create an empty game object
- attach the script to the empty game object
- select the button and add an onClick event
- drag the empty game object to the onClick event
- select the function ClickButton from the dropdown menu
- now when you click the button, it will print "Button Clicked" in the console

### Toggle
- create a toggle in the canvas
- update the text of the toggle to "on/off"
- without a script, you can change run and toggle the checkbox
- for with a script, create a new script
```csharp
using UnityEngine;
using UnityEngine.UI;

public class Prac4_2 : MonoBehaviour
{
    public Toggle toggle;
    void Start()
    {
        toggle.onValueChanged.AddListener(ToggleChnaged);
    }

    void ToggleChnaged(bool isOn)
    {
        if(isOn)
        {
            Debug.Log("Toggle is On");
        }
        else
        {
            Debug.Log("Toggle is Off");
        }
    }
}
```
- now create an empty game object - name it ToggleManager
- attach the script to the ToggleManager
- select the ToggleManager and drag the toggle to the toggle field in the inspector
- now in toggle add onValueChanged event and drag the ToggleManager to the event.
- run the game and toggle the checkbox to see the result in the console

### Slider
- create a slider in the canvas
- update the text of the slider to "Volume"
- min value to 0 and max value to 100
- value to 50
- can test ot out without a script and it will the value in the inspector
- for with a script, create a new script
```csharp
using UnityEngine;
using UnityEngine.UI;

public class Prac4_3 : MonoBehaviour
{
    public Slider slider;
    void Start()
    {
        slider.onValueChanged.AddListener(SliderChanged);
    }

    void SliderChanged(float value)
    {
        Debug.Log("Slider Value: " + value);
    }
}
```
- now create an empty game object - name it SliderManager
- attach the script to the SliderManager
- select the SliderManager and drag the slider to the slider field in the inspector
- now in slider add onValueChanged event and drag the SliderManager to the event.
- run the game and change the slider value to see the result in the console

## Prac 5
### Surface Effector
- create a new 2D project
- create a new sprite - square - name it "Platform" and stretch it to make a platform
- add a box collider 2D & Surface Effector 2D to the platform
- check the "used by effector" in the box collider 2D
- increase the speed in the surface effector 2D
- create a new sprite - circle - name it "Player"
- add a rigidbody 2D & circle collider 2D to the player
- press play and test the player

### Platform Effector
- create a new 2D project
- create a new sprite - square - name it "Platform" and stretch it to make a platform
- add a box collider 2D & Platform Effector 2D to the platform
- check the "Use One Way" in the Platform Effector 2D
- create a new sprite - circle - name it "Player"
- add a rigidbody 2D & circle collider 2D to the player
- press play and test the player 
- can also a script for jump and move to the player
```csharp
using UnityEngine;

public class Prac2_2 : MonoBehaviour
{
    public float speed = 1f;
    private Rigidbody2D rb;
    void Start()
    {
        rb = GetComponent<Rigidbody2D>();
    }

    void Update()
    {
        float move = Input.GetAxisRaw("Horizontal");
        rb.linearVelocity = new Vector2(move * speed, rb.linearVelocity.y);

        if (Input.GetKeyDown(KeyCode.Space))
        {
            rb.linearVelocity = new Vector2(rb.linearVelocity.x, speed);
        }
    }
}
```

### Area Effector
- create a new 2D project
- create a new sprite - square - name it "Platform" and stretch it to make a platform
- add a box collider 2D & Surface Effector 2D to the platform
- add a new 2D sprite - square - name it "Obstacle" 
- add a area effector 2D and a box collider 2D to the obstacle
- add a new 2D sprite - circle - name it "Player"
- add a rigidbody 2D & circle collider 2D to the player
- add a script for jump and move to the player
```csharp
using UnityEngine;

public class Prac5_1 : MonoBehaviour
{
    public float force = 2f;
    public Rigidbody2D rb;
    void Start()
    {
        rb = GetComponent<Rigidbody2D>();
    }

    void Update()
    {
        float moveX = Input.GetAxis("Horizontal");
        rb.linearVelocity = new Vector2(moveX * force, rb.linearVelocity.y);
        if (Input.GetKeyDown(KeyCode.Space))
        {
            rb.linearVelocity = new Vector2(rb.linearVelocity.x, force);
        }
    }
}
```
- add the script to the player and drag the player to the rb field in the inspector
- press play and test the player

### Buoyancy Effector
- create a new 2D project
- create a new sprite - square - name it "Water" and stretch it to make a
- add a box collider 2D & Buoyancy Effector 2D to the water
- in box collider 2D check the "used by effector" and "is trigger"
- in buoyancy effector 2D increase the density to 30,  the surface level to 0.5, linear damping to 3 and angular damping to 1
- create a new sprite - circle - name it "Player"
- add a rigidbody 2D & circle collider 2D to the player
- add a script for jump and move to the player
```csharp
using UnityEngine;

public class Prac5_1 : MonoBehaviour
{
    public float force = 2f;
    public Rigidbody2D rb;
    void Start()
    {
        rb = GetComponent<Rigidbody2D>();
    }

    void Update()
    {
        float moveX = Input.GetAxis("Horizontal");
        rb.linearVelocity = new Vector2(moveX * force, rb.linearVelocity.y);
        if (Input.GetKeyDown(KeyCode.Space))
        {
            rb.linearVelocity = new Vector2(rb.linearVelocity.x, force);
        }
    }
}
```
- add the script to the player and drag the player to the rb field in the inspector
- press play and test the player in the water

### Sound 
- create a new 2D project
- create a new empty game object - name it "Sound"
- add an audio source component to the sound game object
- add the audio clip to the audio source component
- check the "play on awake" and "loop" in the audio source component
- press play and test the sound

## Prac 6 - Scene Management
### Load Scene
- create a new 2D project
- create a new scene - name it "Scene1"
- create a new scene - name it "Scene2"
- in scene1 create a new canvas and a button in the canvas
- update the text of the button to "Go to Scene 2"
- create a new script - name it "SceneLoader"
```csharp
using UnityEngine;
using UnityEngine.SceneManagement;

public class Prac6_1 : MonoBehaviour
{
    public void LoadGameScene()
    {
        SceneManager.LoadScene("Prac4");
    }
}
```
- now create an empty game object - name it "SceneLoader"
- attach the script to the SceneLoader
- select the button and add an onClick event
- drag the SceneLoader to the onClick event
- select the function LoadGameScene from the dropdown menu
- press play and click the button to load scene 2

### Additive Load Scene
- create a new 2D project
- create a new scene - name it "Scene1"
- create a new scene - name it "Scene2"
- create a new scene - name it "Scene3"
- in scene1 create a new canvas and a button in the canvas
- update the text of the button to "Go to Scene 2"
- create a new script - name it "SceneLoader"
```csharp
using System.Collections;
using UnityEngine;
using UnityEngine.SceneManagement;

public class Prac6_1 : MonoBehaviour
{
    public void LoadAdditiveScene()
    {
        SceneManager.LoadScene("Prac5", LoadSceneMode.Additive);
    }
}
```
- now create an empty game object - name it "SceneLoader"
- attach the script to the SceneLoader
- select the button and add an onClick event
- drag the SceneLoader to the onClick event
- select the function LoadAdditiveScene from the dropdown menu
- press play and click the button to load scene 2 additively

### Async Load Scene
- create a new 2D project
- create a new scene - name it "Scene1"
- create a new scene - name it "Scene2"
- in scene1 create a new canvas and a button in the canvas
- update the text of the button to "Go to Scene 2"
- create a new script - name it "SceneLoader"
```csharp
using System.Collections;
using UnityEngine;
using UnityEngine.SceneManagement;

public class Prac6_1 : MonoBehaviour
{
    public void LoadAsyncScene()
    {
        StartCoroutine(LoadSceneAsync());
    }

    IEnumerator LoadSceneAsync()
    {
        AsyncOperation op = SceneManager.LoadSceneAsync("Prac1_3");
        while (!op.isDone)
        {
            Debug.Log($"Loading progress: {op.progress * 100}%");
            yield return null;
        }
    }
}
```
- now create an empty game object - name it "SceneLoader"
- attach the script to the SceneLoader
- select the button and add an onClick event
- drag the SceneLoader to the onClick event
- select the function LoadAsyncScene from the dropdown menu
- press play and click the button to load scene 2 asynchronously and see the loading progress in the console


## Prac 7 - 3D
### Range Checker - Manually
- create a new 3D project
- add a new 3D object - cube - name it "Player"
- add a new 3D object - sphere - name it "Enemy"
- add a new script - name it "RangeChecker"
```csharp
using UnityEngine;

public class Prac7_1 : MonoBehaviour
{
    [SerializeField] Transform target;
    [SerializeField] float range = 5f;
    private bool targetInRange = false;

    void Update()
    {
        var dist = (target.position - transform.position).magnitude;
        if (dist < range && !targetInRange)
        {
            Debug.LogFormat("Target entered range: {0}: {1}", dist, target.name);
            targetInRange = true;
        } else if (dist >= range && targetInRange)
        {
            Debug.LogFormat("Target left range: {0}: {1}", dist, target.name);
            targetInRange = false;
        }
    }
}
```
- attach the script to the player
- select the player and drag the enemy to the target field in the inspector
- press play and move the enemy to see the result in the console

### Range Checker - With Arrow keys
- create a new 3D project
- add a new 3D object - cube - name it "Player"
- add a new 3D object - sphere - name it "Enemy"
- add a new script - name it "RangeChecker"
```csharp
using UnityEngine;
using UnityEngine.InputSystem;

public class Prac7_2 : MonoBehaviour
{
    [SerializeField] Transform target;
    [SerializeField] float range = 2f;
    [SerializeField] float speed = 5f;
    bool targetInRange = false;

    void Update()
    {
        handleMovement();
        handleRangeCheck();        
    }

    void handleMovement()
    {
        Vector2 moveInput = Vector2.zero;
        var keyboard = Keyboard.current;

        if (keyboard != null)
        {
            if (keyboard.wKey.isPressed) moveInput.y += 1;
            if (keyboard.sKey.isPressed) moveInput.y -= 1;
            if (keyboard.aKey.isPressed) moveInput.x -= 1;
            if (keyboard.dKey.isPressed) moveInput.x += 1;
        }

        Vector3 direction = new Vector3(moveInput.x, 0f, moveInput.y).normalized;
        transform.Translate(direction * speed * Time.deltaTime, Space.World);

        if (direction.sqrMagnitude > 0.01f)
        {
            transform.forward = direction;
        }
    }

    void handleRangeCheck()
    {
        var dist = (target.position - transform.position).magnitude;
        if (dist < range && !targetInRange)
        {
            Debug.LogFormat("Target entered range: {0}: {1}", dist, target.name);
            targetInRange = true;
        } else if (dist >= range && targetInRange)
        {
            Debug.LogFormat("Target left range: {0}: {1}", dist, target.name);
            targetInRange = false;
        }
    }
}
```
- attach the script to the player
- select the player and drag the enemy to the target field in the inspector
- press play and use the arrow keys to move the player and see the result in the console when the enemy enters or leaves the range 

### Angle Checker
- create a new 3D project
- add a new 3D object - cube - name it "Player"
- add a new 3D object - sphere - name it "Enemy"
- add a new script - name it "AngleChecker"
```csharp
```

## Prac 8
### Simple 3D game 
- create a new 3D project
- create a new 3D object - capsule - name it "Player"
- create a new 3D object - capsule - name it "enemy"
- create a new 3D object - plane - name it "Grass"
- create a new 3D object - cube - name it "Goal"
- place and scale them all accordinly to your liking
- on player add a rigidbody and a capsule collider
- on enemy add a navmesh agent and a capsule collider
- create a empty game object - name it "NavMesh" and add a navmesh surface component to it
- bake the navmesh surface
- create a new script for player movement
```csharp
using UnityEngine;

public class Player : MonoBehaviour
{
    public float speed = 5f;
    Rigidbody rb;
    void Start()
    {
        rb = GetComponent<Rigidbody>();
    }

    void FixedUpdate()
    {
        float h = Input.GetAxis("Horizontal");
        float v = Input.GetAxis("Vertical");

        Vector3 movement = new Vector3(h, 0f, v) * speed;
        rb.linearVelocity = new Vector3(movement.x, rb.linearVelocity.y, movement.z);        
    }
}
```
- create a new script for enemy movement
```csharp
using UnityEngine;
using UnityEngine.AI;

public class Enemy : MonoBehaviour
{
    public Transform target;
    public float speed = 3.5f;
    private NavMeshAgent agent;
    void Start()
    {
        agent = GetComponent<NavMeshAgent>();
        agent.speed = speed;
    }

    void Update()
    {
        if (target != null)
        {
            agent.SetDestination(target.position);
        }       
    }
}
```

- create a new script for goal
```csharp
using UnityEngine;

public class Goal : MonoBehaviour
{
    private void OnTriggerEnter(Collider other)
    {
        if (other.CompareTag("Player"))
        {
            Debug.Log("Goal reached!");
            Debug.LogFormat("Player Wins!!");
            Time.timeScale = 0f;
        } else if (other.CompareTag("Enemy"))
        {
            Debug.Log("Goal reached!");
            Debug.LogFormat("Player Loses!!");
            Time.timeScale = 0f;
        }
    }    
}
```
- create a new script for game manager
```csharp
using UnityEngine;

public class GameManager : MonoBehaviour
{
    public GameObject player;
    public GameObject enemy;
    public GameObject goal;

    void Update()
    {
        float playerDist = Vector3.Distance(player.transform.position, goal.transform.position);
        float enemyDist = Vector3.Distance(enemy.transform.position, goal.transform.position);        


        if (playerDist < 5f)
        {
            Debug.Log("Player wins!");
            QuitGame();
        } else if (enemyDist < 5f)
        {
            Debug.Log("Enemy wins!");
            QuitGame();
        }
    }

    void QuitGame()
    {
        Application.Quit();
        UnityEditor.EditorApplication.isPlaying = false;
    }
}
```
- attach the player script to the player game object and set the tag to "Player"
- attach the enemy script to the enemy game object and set the tag to "Enemy" and set the target as the goal in the inspector
- attach the goal script to the goal game object and add a box collider and check the "is trigger" in the box collider
- create an empty game object - name it "GameManager" and attach the game manager script to it and set the player, enemy and goal fields in the inspector
- press play and test the game by moving the player towards the goal while avoiding the enemy.

### AI Chases Player 
- create a new 3D project
- create a new 3D object - capsule - name it "Player"
- create a new 3D object - capsule - name it "Enemy"
- create a new 3D object - plane - name it "Grass"
- place and scale them all accordinly to your liking
- on player add a rigidbody and a capsule collider
- on enemy add a navmesh agent and a capsule collider
- create a empty game object - name it "NavMesh" and add a navmesh surface
and bake the navmesh surface
- create a new script for player movement
```csharp
using UnityEngine;

public class Player : MonoBehaviour
{
    public float speed = 5f;
    void Update()
    {
        float h = Input.GetAxis("Horizontal");
        float v = Input.GetAxis("Vertical");

        Vector3 movement = new Vector3(h, 0, v);
        transform.Translate(movement * speed * Time.deltaTime, Space.World);
    }
}
```

- create a new script for enemy movement
```csharp
using UnityEngine;
using UnityEngine.AI;

public class Enemy1 : MonoBehaviour
{
    public Transform target;
    public float detectRange = 5f;
    private NavMeshAgent agent;
    void Start()
    {
       agent = GetComponent<NavMeshAgent>();
    }

    void Update()
    {
         if(target == null) return;
        float distance = Vector3.Distance(transform.position, target.position);

        if(distance <= detectRange)
        {
            agent.SetDestination(target.position);
            Debug.Log("Enemy is chasing the player!");

            if (distance < 2f)
            {
                Debug.Log("Enemy has caught the player!");
                StopGame();
            }
        }
    }

    void StopGame()
    {
        Debug.Log("Game Over!");
        #if UNITY_EDITOR
            UnityEditor.EditorApplication.isPlaying = false;
        #else
            Application.Quit();
        #endif
    }
}
```
- attach the player script to the player game object and set the tag to "Player"
- attach the enemy script to the enemy game object and set the tag to "Enemy" and set the target as the player in the inspector
- press play and test the game by moving the player around and see if the enemy chases the player when it is within the detect range and if the enemy catches the player when it is within 2 units of distance.

