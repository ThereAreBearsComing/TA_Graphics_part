# C#初级编程基础

脚本应该被视为，Unity中的行为组件。
```C#
using UnityEngine;
using System.Collections;

public class ExampleBehaviourScript : MonoBehaviour
{
    void Update()
    {
        if (Input.GetKeyDown(KeyCode.R))
        {
            GetComponent<Renderer> ().material.color = Color.red;
        }
        if (Input.GetKeyDown(KeyCode.G))
        {
            GetComponent<Renderer>().material.color = Color.green;
        }
        if (Input.GetKeyDown(KeyCode.B))
        {
            GetComponent<Renderer>().material.color = Color.blue;
        }
    }
}
```

## 0.0 变量和函数
我们可以将变量看作包含信息的盒子，当然不同类型的信息，我没也应该准备不同的盒子。当我们定义变量时赢先确定，我们需要什么类型的盒子。

```C#
using UnityEngine;
using System.Collections;

public class VariablesAndFunctions : MonoBehaviour
{   
    // 变量1
    int myInt = 5; # int类型，为integer 整数， 之后myInt为命名，之后=声明变量，变量想要发挥作用，则应该被套在整个函数中
    
    // 函数1
    // Start()函数：当这个脚本绑定的对象进入场景时就会调用此函数
    void Start ()
    {
        myInt = MultiplyByTwo(myInt); // 调用了下边定义的函数，函数名为MultiplyByTwo()
        Debug.Log (myInt); // 用此获取游戏中的任意变量值，
    }
    // 当函数不返回(return)任何值时候，函数类型为void
    
    // 函数2
    // 当我们编写函数时，我们可以指定一个返回类型
    int MultiplyByTwo (int number)    // MultiplyByTwo为函数自定义名称，括号内(int number)为Input类型，当输入为此类型时，函数才会返回对应的结果，当然其本身也是临时变量
    {
        // 变量2
        int ret;
        ret = number * 2;
        return ret;
    }
}
```

## 1.0 脚本编写约定和语法
```C#
using UnityEngine;
using System.Collections;

public class BasicSyntax : MonoBehaviour
{
    void Start ()
    {
        // This line is there to tell me the X position of my object
        
        /* Hi there!
         * This is two lines!
         * */
         
         /*
        多行注释
        */
         
        Debug.Log(transform.position.x); // 这里Debug为“国家”的话,Log为该国家的“城市”。即前边的元素，所以这里x为'position'市中的某街道
        
        if(transform.position.y <= 5f)
        {
            Debug.Log ("I'm about to hit the ground!");
        }
        
    }
}
```
`;`分号的规则和CG，HLSL类似


## 2.0 IF语句

```C#
using UnityEngine;
using System.Collections;

public class IfStatements : MonoBehaviour
{
    float coffeeTemperature = 85.0f;
    float hotLimitTemperature = 70.0f;
    float coldLimitTemperature = 40.0f;
    

    void Update ()
    {
        if(Input.GetKeyDown(KeyCode.Space))
            TemperatureTest();
        
        coffeeTemperature -= Time.deltaTime * 5f;
    }
    
    
    void TemperatureTest ()
    {
        // 如果咖啡的温度高于最热的饮用温度...
        if(coffeeTemperature > hotLimitTemperature) // 条件一
        {
            // ... 执行此操作。
            print("Coffee is too hot.");
        }
        // 如果不是，但咖啡的温度低于最冷的饮用温度...
        else if(coffeeTemperature < coldLimitTemperature) // 条件二
        {
            // ... 执行此操作。
            print("Coffee is too cold.");
        }
        // 如果两者都不是，则...
        else                                                // 条件三
        {
            // ... 执行此操作。
            print("Coffee is just right.");
        }
    }
}
```

## 3.0 循环
Loop即重复操作的方式。

### WhileLoop
```C#
using UnityEngine;
using System.Collections;

public class WhileLoop : MonoBehaviour
{
    int cupsInTheSink = 4;
    
    
    void Start ()
    {
        while(cupsInTheSink > 0)
        {
            Debug.Log ("I've washed a cup!");
            cupsInTheSink--;
        }
    }
}
```


### DoWhileLoop
与WhileLoop不同之处是，WhileLoop在循环主体之前检验条件，DoWhileLoop在结束时菜开始检验条件。这意味着DoWhileLoop将至少运行一次。
```C#
using UnityEngine;
using System.Collections;

public class DoWhileLoop : MonoBehaviour 
{
    void Start()
    {
        bool shouldContinue = false;
        
        do
        {
            print ("Hello World");
            
        }while(shouldContinue == true);
    }
}
```

### ForLoop
ForLoop是最常见最灵活的循环，ForLoop利用可控的数量的迭代创建循环，就功能而言他会先检查循环中的条件，
```C#
using UnityEngine;
using System.Collections;

public class ForLoop : MonoBehaviour
{
    int numEnemies = 3;
    
    
    void Start ()
    {
        for(int i = 0; i < numEnemies; i++) // i为变量迭代子，用于计算迭代次数。接着为条件，must be true。最后为每次循环后对迭代子的更新处理。
        {
            Debug.Log("Creating enemy number: " + i);
        }
    }
}
```

### ForeachLoop
```C#
using UnityEngine;
using System.Collections;

public class ForeachLoop : MonoBehaviour 
{   
    void Start () 
    {
        string[] strings = new string[3];
        
        strings[0] = "First string";
        strings[1] = "Second string";
        strings[2] = "Third string";
        
        foreach(string item in strings)
        {
            print (item);
        }
    }
}
```

## 4.0 作用域和访问修饰符
变量作用域指，代码中可以使用这个变量的区域。变量局限于代码之中，而代码域表示为花括号。如果其他脚本希望访问其他域中的变量则，需要public变量，否则为private。如果被public则意味着即使在class外，也可以被访问。这也意味着在Inspector中也将可以显示和编辑此变量！！！如果没指定变量为public或private，则默认为private。

### ScopeAndAccessModifiers
```C#
using UnityEngine;
using System.Collections;

public class ScopeAndAccessModifiers : MonoBehaviour
{
    public int alpha = 5;
    
    
    private int beta = 0;
    private int gamma = 5;
    
    
    private AnotherClass myOtherClass;
    
    
    void Start ()
    {
        alpha = 29; // 一但运行这个值将会覆盖Inspector的输入，但在在运行中，在次在Inspector修改的话，他就会再次覆盖脚本中的29。
                    // 但是重要的是在退出运行模式后运行中Inspector修改的数值将被重置，且不会保存到Inspector中
        
        myOtherClass = new AnotherClass();
        myOtherClass.FruitMachine(alpha, myOtherClass.apples); //只能用apples和banana，因为就这俩是public
    }
    
    
    void Example (int pens, int crayons)
    {
        int answer;
        answer = pens * crayons * alpha;
        Debug.Log(answer);
    }
    
    
    void Update ()
    {
        Debug.Log("Alpha is set to: " + alpha);
    }
}
```
<br>![image](https://user-images.githubusercontent.com/74708198/201528172-4e1be304-d36c-484a-9c6b-60ac99db1deb.png)


### AnotherClass
```C#
using UnityEngine;
using System.Collections;

public class AnotherClass
{
    // 其它脚本可以访问
    public int apples;
    public int bananas;
    
    // 其它脚本无法访问
    private int stapler;
    private int sellotape; 
    
    
    public void FruitMachine (int a, int b)
    {
        int answer;
        answer = a + b;
        Debug.Log("Fruit total: " + answer);
    }
    
    
    private void OfficeSort (int a, int b)
    {
        int answer;
        answer = a + b;
        Debug.Log("Office Supplies total: " + answer);
    }
}
```

## 5.0 Awake 和 Start
Awake和Start都是在加载脚本时自动调用的两个函数，其中Awake即使脚本组件未启用也没关系，所以特别适合与初始化之间设置任何引用。 Start则在Awake之后调用，而且直接在首次更新之前调用，但前提是已经启用了脚本组件，即启用脚本组件的情况下，可以使用Start启动任何所需操作，而且是直接在首次更新之前调用，但前提必须是脚本已经启用（即在Inspector中被勾选）。即在启用脚本组件的情况下，你可以用Start启动任意所需操作。这样就可以将初始化部分的代码，延迟到真正需要的时候运行。
<br>例如，有一个敌方角色进入游戏，并使用了Awake获得了分配的弹药，但是想要设计，需要在启动脚本组件时，使用Start在定义时间实现射击，但需要注意Start喝Awake在一个对象绑定脚本的生命周期内只能调用一次，因此不能通过禁用和重新启用脚本来重复执行Start函数。

```C#
using UnityEngine;
using System.Collections;

public class AwakeAndStart : MonoBehaviour
{
    void Awake ()
    {
        Debug.Log("Awake called.");
    }
    
    
    void Start ()
    {
        Debug.Log("Start called.");
    }
}
```
<br>给游戏内物体挂在此脚本，当在Inspector内不勾选时，运行：
<br>![image](https://user-images.githubusercontent.com/74708198/201535411-7e3f6cc7-261c-460b-8d2b-f1c3c895a0ca.png)
<br>则在Console只会看到Awake函数。
<br>![image](https://user-images.githubusercontent.com/74708198/201535441-75638974-078d-4158-be99-1bbe51182fbe.png)
<br>再回去Inspector内勾选脚本，重新运行：
<br>![image](https://user-images.githubusercontent.com/74708198/201535511-8238fd24-2cb9-4fae-a2ff-9a7642e5127e.png)
<br>则在Console会看到Awake和Start两个函数。
<br>![image](https://user-images.githubusercontent.com/74708198/201535519-60a54e8f-7760-4a63-b550-6787561aeda8.png)

6.0 Update 和 FixedUpdate
Update是最常用的函数之一，在每个使用它的脚本中每帧调用一次。基本上只需要变化或调整都需要使用Update来实现。例如，非物理对象的移动，简单的计时器，输入检测等等。一般都在Update中完成。
注意Update并不是固定时间，如果这一帧比下一帧处理时间长，那Update时间也会长。
<br>FixedUpdate函数与Update类似，但是FixedUpdate是按固定时间调用，调用FixedUpdate后会立即进行任何必要的物理计算，因此任何影响刚体（即物理对象）的动作都应该使用FixedUpdate执行而不是Update。在FixedUpdate循环中编写的物理脚本时，最好使用力来定义移动。

```C#
using UnityEngine;
using System.Collections;

public class UpdateAndFixedUpdate : MonoBehaviour
{
    void FixedUpdate ()
    {
        //Called every physics step
        // FixedUpdate intervals are consistent
        // Used forvregular updates such as:
        // Adjusting physics (Ridgidbody) objects
        Debug.Log("FixedUpdate time :" + Time.deltaTime);
    }
    
    
    void Update ()
    {
        Debug.Log("Update time :" + Time.deltaTime);
    }
}
```
<br>当执行此脚本后，所有的FixUpdate都一样0.02，并且可以折叠为一个，而Update明显不一样且不能折叠为一个。
<br>![image](https://user-images.githubusercontent.com/74708198/201538518-ff3a3851-047c-424e-b488-731a5cca6781.png)
