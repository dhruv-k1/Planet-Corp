# mdg-assignment-
A survival game in whoch player has some amount of energy at the start which decreases on running and jumpoing and increases on colliding with food items. Player dies when it collides with enemies or when the energy amount becomes 0.


#Player Script:

using System.Collections;
using System.Collections.Generic;
using UnityEngine;
using UnityEngine.UI;
using UnityEngine.SceneManagement; //to call SceneManager function which will allow us to navigate between scenes. 

public class Player : MonoBehaviour
{

    private float moveForce=10f; //variable that will help in player movement
    
    [SerializeField]
    private float jumpForce=11f; //a player needs a force to make it jump and this variable will be used in that. 
    private float movementX;  //to calculate change in x coordinate of player
      [SerializeField]
    private Rigidbody2D myBody; //adding 'rigidbody 2D' component to the player which will make gravity act on it, allow it to experience force, etc. 
    [SerializeField]
    private SpriteRenderer sr; //Sprite renderer will add the player sprite to our scene. 
    [SerializeField]
    private Animator anim; // to animate the player
    private string RUN_ANIMATION = "Run final"; //to access 'run animation'
    


    private bool isGrounded=true; //declaring a variable which will be used to make player jump only when it is on ground. 
    //declaring tags:
    private string GROUND_TAG="Ground"; 
    private string ENEMY_TAG="Enemy";
    private string FOOD_TAG="Food";




    //declaring variables which will be used to control energy bar:
    [SerializeField] private float _maxEnergy=10f; //setting the max energy to 10.
    private float _thresholdEnergy=9f; //setting the threshold energy to 9.
    private float _currentEnergy; //player's energy status at a given time. 
    [SerializeField] private EnergyBar _energybar;




    private void awake(){
    
    }


    // Start is called before the first frame update
    void Start()
    {
       _currentEnergy=8f; //game will start with player having initial energy=8.
       _energybar.UpdateEnergyBar(_maxEnergy,_currentEnergy); //calling 'UpdateEnergyBar' function to update the player's energy bar according to current energy
    }

    void consumeEnergy(){    //function that will determine the consequence on player's speed depending on it's energy status
        
     
        if (_currentEnergy>_thresholdEnergy){
             moveForce=5f; //if energy is greater than the threshold energy, the player's speed decreases to 5
             Debug.Log("Energy greater than threshold energy! Speed reduced to 5.");
             _energybar.UpdateEnergyBar(_maxEnergy,_currentEnergy);
        }

        if(_currentEnergy<=_thresholdEnergy && _currentEnergy>3f)  {
            moveForce=10f;
            Debug.Log("Speed normal");
            _energybar.UpdateEnergyBar(_maxEnergy,_currentEnergy);
        } 
        
        else if (_currentEnergy<=3f){
            moveForce=3f;
            Debug.Log("Energy is low! Speed reduced to 3.");
            _energybar.UpdateEnergyBar(_maxEnergy,_currentEnergy);
        }
           

        else if (_currentEnergy<=0f){
            Destroy(gameObject); //gameObject, ie, the player is destroyed when energy bar reaches 0 and the game concludes. 
            SceneManager.LoadScene("GameOver"); //GameOver scene is called
        }
            
   
        }
    // Update is called once per frame 
    // All the functions inside the Update will be called once per frame. 
    void Update()
    {
        PlayerMoveKeyboard();
        AnimatePlayer();
         PlayerJump();
         consumeEnergy();
    }

   
    //function to determine player's movement:
    void PlayerMoveKeyboard(){

     movementX=Input.GetAxisRaw("Horizontal"); //change in x coordinate, ie, movementX can take values between -1, 0 & 1 when the player inputs horizontal commands (D, A, Right arrow or Left arrow) 
     transform.position += new Vector3(movementX,0f,0f)*moveForce*Time.deltaTime; //determining the position of player by adding the (change in x) *(moveforce) *(time.delta time), where time refers to amount of seconds for which key is pressed and delta time refers to the time difference between each frame change. 
          
    }

    //function to control player's animation:
    void AnimatePlayer(){
        if(movementX>0){
            anim.SetBool(RUN_ANIMATION, true);  //function that will enable the transition between idle and run state
            sr.flipX=false;
             _currentEnergy -= 0.0005f;  //energy decrement while running
        }
        else if(movementX<0){
            anim.SetBool(RUN_ANIMATION, true);
            sr.flipX=true; //this function flips the player sprite so that when it is running in -x direction, the player also turns in the -x direction
             _currentEnergy -= 0.0005f;
        }

        else
         {anim.SetBool(RUN_ANIMATION, false);
         }


    
    }
    
    //functioj to determine player's jump:
    void PlayerJump(){
        if(Input.GetKeyDown(KeyCode.Space) && isGrounded){   //it is called when user inputs space key and when the player is grounded. 
            isGrounded=false;  //switching the variable to false so that only one jump is executed at a time. 
            myBody.AddForce(new Vector2(0f, jumpForce), ForceMode2D.Impulse);  //adding force to player to make it reach at a height
          
            
        _currentEnergy -= 0.025f; //energy decrement while jumping
           
        }
        
        
    }

    //creating a function to determine player collisions
    //every object has a box collider component(ie, a component that sets a boundary around object to detect collsiions) and given their respective tag for example enemy has enemy tag, food items have good tag, etc. 
    private void OnCollisionEnter2D(Collision2D collision){
        if (collision.gameObject.CompareTag(GROUND_TAG)){  //condition to check whether the player has collided with ground object
            isGrounded = true;
        }

        if (collision.gameObject.CompareTag(ENEMY_TAG)){ //condition to check whether the player has collided with ground object
            Destroy(gameObject);
            SceneManager.LoadScene("GameOver");
        }

        if (collision.gameObject.CompareTag(FOOD_TAG)){  //condition to check whether the player has collided with ground object
            _currentEnergy +=0.5f; //energy increment on collidong with food item
            Destroy(collision.gameObject);
           
        }


    }



}
