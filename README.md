## Flashlight Toggle Of On
```lua
using UnityEngine;
using System.Collections;

public class Flashlight : MonoBehaviour {
	public AudioClip switchSound;
	
	void Update () {
		if(Input.GetKeyDown(KeyCode.F))
		{
			audio.PlayOneShot(switchSound);
			
			if(light.enabled)
			{
				light.enabled = false;
			}
			else
			{
				light.enabled = true;
			}
		}
	}
}
```
## jumpscare
```lua
using UnityEngine;
using System.Collections;
using UnityEngine.UI;

public class JumpScare : MonoBehaviour {

    public Image image;
    public AudioSource audioSource;

    void OnTriggerEnter(Collider other)
    {
        StartCoroutine(JumpScare_CR());
    }

    IEnumerator JumpScare_CR()
    {
        image.enabled = true;
        audioSource.Play();
        yield return new WaitForSeconds(0.5f);
        image.enabled = false;
    }
}
```
## Shooting Gun
```lua
using UnityEngine;
using System.Collections;

public class ShootDemo : MonoBehaviour {

	public Rigidbody projectile;

	public float speed = 20;



	// Use this for initialization
	void Start () {
	
	}
	
	// Update is called once per frame
	void Update () {

		if (Input.GetButtonDown("fire1"))
		{ 
			Rigidbody instantiatedProjectile = Instantiate(projectile,
			                                               transform.position,
			                                               transform.rotation)
				as Rigidbody;

			instantiatedProjectile.velocity = transform.TransformDirection(new Vector3(0, 0,speed));

		}
	}
}
```
## interact object 1
```lua
using UnityEngine;

public class Interactable : MonoBehaviour
{
    public float radius = 3f;
    public float maxSelectDistance = 25f;
    bool isFocus = false;
    Transform player;
    bool hasInteracted = false;
    public GameObject cursorImage;
    public GameObject mainCameraObject;
    public Renderer selectedObject;
    Camera cam;
    Vector3 heightOffsetPos;
    Vector3 screenPos;
    Vector3 twoDimPos;

    void Start() {
        isFocus = false;
        player = GameObject.Find("MouseKnight").transform;
        cursorImage = GameObject.FindWithTag("Select Cursor");
        cursorImage.SetActive(true);
        cam = GameObject.FindWithTag("MainCamera").GetComponent<Camera>();
        //selected object should be whatever the player has focused.
    }

   

    public virtual void Interact() {
    }
    void Update() {
        //used to test the status of isFocus
        if (isFocus) {
            print(player.GetComponent<PlayerInteraction>().focus.name);
        }
        //used for tracking object's position
        selectedObject = GetComponent<Renderer>();

        initInteract();
        //placeCursor();
    }

    //checks if we're close enough to interact & if we've already interacted
    public void initInteract() {
        if (isFocus && !hasInteracted) {
            float distance = Vector3.Distance(player.transform.position, transform.position);
            if (distance <= radius) {
                Interact();
            }
        }
        hasInteracted = true;
    }

    //turns on isFocus and is used in the PlayerInteraction Script
    public void OnFocused (Transform playerTransform) {
        isFocus = true;
        print("isFocus : " + isFocus);
        player = playerTransform;
        hasInteracted = false;

    }

    //turns off isFocus and is used in the PlayerInteraction Script
    public void OnDefocused() {
        isFocus = false;
        print("isFocus : "+ isFocus);
        player = null;
        hasInteracted = false;
    }
}
```
## interact 2
```lua
using UnityEngine;
using System.Collections;

public class PlayerInteraction : MonoBehaviour {
    public GameObject mainCameraObject;
    private Camera cam;
    public Interactable focus = null;
    Transform player;

    void Start() {
        cam = mainCameraObject.GetComponent<Camera>();
    }

    void Update() {
        if (Input.GetMouseButtonDown(0)) {
            Ray ray = cam.ScreenPointToRay(Input.mousePosition);
            RaycastHit hit;

            if (Physics.Raycast(ray, out hit)) {
                Interactable interactable = hit.collider.GetComponent<Interactable>();

                if (interactable != null) {
                    SetFocus(interactable);
                   
                } else RemoveFocus();
            }
        }
    }

    void SetFocus (Interactable newFocus) {
        if (newFocus != focus) {
            if (focus != null) {
                focus.OnDefocused();
            }
            focus = newFocus;
        }
       
        newFocus.OnFocused(transform);
    }

    void RemoveFocus() {
        if (focus != null){
            focus.OnDefocused();
        }
        focus = null;
       
    }
}
```
## walk 1
```lua
void walk(){
	Quaternion rotation = Quaternion.LookRotation (wayPoint [currentWayPoint].position - transform.position);
	transform.rotation = Quaternion.Slerp (transform.rotation, rotation, Time.deltaTime * rotationSpeed);

	Vector2 wayPointDirection = wayPoint[currentWayPoint].position - transform.position;
	float speedElement = Vector2.Dot(wayPointDirection.normalized, transform.forward);
	float speed = acceleration * speedElement;
	transform.Translate (0, 0, Time.deltaTime * speed);
} 

void onTriggerEnter(Collider collider){
	if (collider.tag == "WayPoint") {
		currentWayPoint ++ ;
	}

}
```
## walk 2
```lua
public Transform[] wayPoint = new Transform[9];
int currentWayPoint = 1;

public float rotationSpeed = 6.0f;
public float acceleration = 1.8f;

// Use this for initialization
void Start () {
	wayPoint [0] = GameObject.Find ("WayPoint1").transform;
	wayPoint [1] = GameObject.Find ("WayPoint2").transform;
	wayPoint [2] = GameObject.Find ("WayPoint3").transform;
	wayPoint [3] = GameObject.Find ("WayPoint4").transform;
	wayPoint [4] = GameObject.Find ("WayPoint5").transform;
	wayPoint [5] = GameObject.Find ("WayPoint6").transform;
	wayPoint [6] = GameObject.Find ("WayPoint7").transform;
	wayPoint [7] = GameObject.Find ("WayPoint8").transform;		
	wayPoint [8] = GameObject.Find ("WayPoint9").transform;

}

// Update is called once per frame
void Update () {
	if (currentWayPoint == 9) {
			Destroy (this.gameObject);
			Player.lives =- 1;
		} 
		else 
			{
```
## walk 3
```lua
public class WayPoints : MonoBehaviour {

	// put the points from unity interface
	public Transform[] wayPointList;

	public int currentWayPoint = 0; 
	Transform targetWayPoint;

	public float speed = 4f;

	// Use this for initialization
	void Start () {

	}
	
	// Update is called once per frame
	void Update () {
		// check if we have somewere to walk
		if(currentWayPoint < this.wayPointList.Length)
		{
			if(targetWayPoint == null)
				targetWayPoint = wayPointList[currentWayPoint];
			walk();
		}
	}

	void walk(){
		// rotate towards the target
		transform.forward = Vector3.RotateTowards(transform.forward, targetWayPoint.position - transform.position, speed*Time.deltaTime, 0.0f);

		// move towards the target
		transform.position = Vector3.MoveTowards(transform.position, targetWayPoint.position,   speed*Time.deltaTime);

		if(transform.position == targetWayPoint.position)
		{
			currentWayPoint ++ ;
			targetWayPoint = wayPointList[currentWayPoint];
		}
	} 
}
```
