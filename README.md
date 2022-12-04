Выполнил: Смирнов Н.М

Группа: ЭВТ-70

Игровой движок: Unity 2021.3.15

Название работы: разработка игры HopHopHop

Лабораторная работа 23

Тема: разработка игры HopHopHop

Цель: создать игру HopHopHop

Ход работы:

1.Выполнение работы

Импортирование ресурсов в проект.

![image](https://user-images.githubusercontent.com/119733911/205502601-760d37c8-152a-42a2-b5a5-25d845bbdd46.png)

Рисунок 23.1 Используя редактор срезов, получаем из набора спрайты 

![image](https://user-images.githubusercontent.com/119733911/205502610-8624f3cb-407a-43d9-a04b-d64d6f2e3be9.png)

Рисунок 23.2

![image](https://user-images.githubusercontent.com/119733911/205502630-29796e98-aabc-42c6-9ce1-14326342f434.png)

Рисунок 23.3 Размещение спрайтов на сцене. 

Скачиваем Cinematic Studio Sample и импортируем в проект.
Настраиваем вид камеры “CM vcam1”, так чтобы в области был виден игрок и немного дальше него объекты.

![image](https://user-images.githubusercontent.com/119733911/205502638-cd6b48d9-5185-4884-9000-6651e586de41.png)

Рисунок 23.4 Настройка камеры следования за игроком.


Листинг 23.1 Player.cs
using UnityEngine;

public class Player : MonoBehaviour
{
    public Vector2 jumpDirection;
    public int jumpForce;
    Rigidbody2D rgbd;
    Vector2 currentSpeed;
    public Vector2 maxSpeed;
    public bool takePlayerInput;
    Animator animator;
    GameManager gameManager;

    void Start()
    {
        rgbd = GetComponent<Rigidbody2D>();
        rgbd.gravityScale = 0;
        animator = GetComponent<Animator>();
        gameManager = FindObjectOfType<GameManager>();
    }

    void Update()
    {   
        if (takePlayerInput) return;
        if (Input.GetMouseButtonDown(0))
        {
            if (rgbd.gravityScale != 1) { rgbd.gravityScale = 1; }
            rgbd.AddForce(jumpDirection * jumpForce * Time.deltaTime);
            ControlSpeed();
        }
    }
    
    void ControlSpeed()
    {
        currentSpeed = rgbd.velocity;
        if (currentSpeed.x != maxSpeed.x) { currentSpeed.x = maxSpeed.x; }
        if (currentSpeed.y != maxSpeed.y) { currentSpeed.y = maxSpeed.y; }
        rgbd.velocity = currentSpeed;
    }

    public void PlayerDead()
    {

        //PlayDead Animation
        //Restart Scene
        Invoke("RestartScene", 2);
        animator.SetTrigger("Dead");
    }

    public void RestartScene()
    {
        gameManager.RestartScene();
    }

}




Листинг 23.2 ObjectHolderInstantiator.cs
using UnityEngine;
public class ObjectHolderInstantiator : MonoBehaviour
{
    public GameObject obstacle;
    public int distanceBetweenObstacle;
    int currentObstaclePosition;
    public Vector2 minMaxYValue;
    
    void Start()
    {
        InstantiateObstacle();
    }
    
    public void InstantiateObstacle()
    {
        for (int i = 0; i < 50; i++)
        {
            currentObstaclePosition += distanceBetweenObstacle;
            GameObject GO =  Instantiate(obstacle, new Vector3(currentObstaclePosition, 0, 0),
            Quaternion.identity) as GameObject;
            GO.transform.GetChild(1).transform.position = 
                new Vector2(GO.transform.GetChild(1).position.x, Random.Range(minMaxYValue.x, minMaxYValue.y));
        }
    }
}

Листинг 23.3 HoopHolder.cs
using UnityEngine;
public class HoopHolder : MonoBehaviour
{
    public Transform obstacle;
    bool obstacleCompleted;
    void Start()
    {
        
    }
    public void OnCollisionEnter2D(Collision2D collision)
    {
        if (collision.collider.tag == "Player")
        {
            //take control of player
            FindObjectOfType<Player>().takePlayerInput = true;
        }
    }
    public void OnTriggerEnter2D(Collider2D collision)
    {
        if (obstacleCompleted) return;
        if (collision.tag == "Player")
        {
            FindObjectOfType<Player>().takePlayerInput = false;
            obstacle.GetComponent<Obstacle>().RemoveObstacle();
            DestroyGameObject();
        }

    }
    public void DestroyGameObject()
    {
        GetComponentInParent<ObstacleHolder>().PlayDeadAnimation();
        Destroy(gameObject);
    }
}

Листинг 23.4 Obstacle.cs
using UnityEngine;
public class Obstacle : MonoBehaviour
{
    void Start()
    {
        
    }

    public void OnCollisionEnter2D(Collision2D collision)
    {
        if (collision.collider.tag == "Player")
        {
            FindObjectOfType<Player>().PlayerDead();
        }
    }
    public void RemoveObstacle()
    {
        Destroy(gameObject);
   }
}

Листинг 23.5 ObstacleHolder.cs
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class ObstacleHolder : MonoBehaviour
{
    Animator animator;

    void Start()
    {
        animator = GetComponent<Animator>();
    }

    public void PlayDeadAnimation()
    {
        animator.SetTrigger("Destroy");
        Destroy(gameObject, 2f);
    }
}

Листинг 22.6 GameManager.cs
using UnityEngine.SceneManagement;
using UnityEngine;

public class GameManager : MonoBehaviour
{
    void Start()
    {
        
    }

    public void RestartScene()
    {
        SceneManager.LoadScene(SceneManager.GetActiveScene().buildIndex);
    }
}

2.Вывод:
В ходе проделанной работы, мы разработали игру HopHopHop
