# mdg-assignment-
A survival game in whoch player has some amount of energy at the start which decreases on running and jumpoing and increases on colliding with food items. Player dies when it collides with enemies or when the energy amount becomes 0.


#Player Script:

using System.Collections;
using System.Collections.Generic;
using UnityEngine;
using UnityEngine.UI;
using UnityEngine.SceneManagement;

public class Player : MonoBehaviour
{

    private float moveForce=10f;
    
    [SerializeField]
    private float jumpForce=11f;
    private float movementX;
      [SerializeField]
    private Rigidbody2D myBody;
    [SerializeField]
    private SpriteRenderer sr;
    [SerializeField]
    private Animator anim;
    private string RUN_ANIMATION = "Run final";
    


    private bool isGrounded=true;
    private string GROUND_TAG="Ground";
    private string ENEMY_TAG="Enemy";
    private string FOOD_TAG="Food";





    [SerializeField] private float _maxEnergy=10f;
    private float _thresholdEnergy=9f;
    private float _currentEnergy;
    [SerializeField] private EnergyBar _energybar;




    private void awake(){
    
    }


    // Start is called before the first frame update
    void Start()
    {
       _currentEnergy=8f;
       _energybar.UpdateEnergyBar(_maxEnergy,_currentEnergy); 
    }

    void consumeEnergy(){
        
     
        if (_currentEnergy>_thresholdEnergy){
             moveForce=5f;
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
            Destroy(gameObject);
            SceneManager.LoadScene("GameOver");
        }
            
   
        }
    // Update is called once per frame
    void Update()
    {
        PlayerMoveKeyboard();
        AnimatePlayer();
         PlayerJump();
         consumeEnergy();
    }

   

    void PlayerMoveKeyboard(){

     movementX=Input.GetAxisRaw("Horizontal");
     transform.position += new Vector3(movementX,0f,0f)*moveForce*Time.deltaTime;
      
      
       
    }


    void AnimatePlayer(){
        if(movementX>0){
            anim.SetBool(RUN_ANIMATION, true);
            sr.flipX=false;
             _currentEnergy -= 0.0005f;
        }
        else if(movementX<0){
            anim.SetBool(RUN_ANIMATION, true);
            sr.flipX=true;
             _currentEnergy -= 0.0005f;
        }

        else
         {anim.SetBool(RUN_ANIMATION, false);
         }


    
    }

    void PlayerJump(){
        if(Input.GetKeyDown(KeyCode.Space) && isGrounded){
            isGrounded=false;
            myBody.AddForce(new Vector2(0f, jumpForce), ForceMode2D.Impulse);
          
            
        _currentEnergy -= 0.025f;
           
        }
        
        
    }

    private void OnCollisionEnter2D(Collision2D collision){
        if (collision.gameObject.CompareTag(GROUND_TAG)){
            isGrounded = true;
        }

        if (collision.gameObject.CompareTag(ENEMY_TAG)){
            Destroy(gameObject);
            SceneManager.LoadScene("GameOver");
        }

        if (collision.gameObject.CompareTag(FOOD_TAG)){
            _currentEnergy +=0.5f;
            Destroy(collision.gameObject);
            Scoring.instance.AddPoint();
        }


    }



}
