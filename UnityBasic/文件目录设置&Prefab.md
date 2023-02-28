# 组织项目目录结构

 ![image](https://user-images.githubusercontent.com/74708198/221208604-f2ac9711-15ef-46e9-b24f-08638b0c293b.png)
* Assets
  * AssetsPackages:
    * Characterss
    * Effects
    * Excels (Infor, Json, xml ..)
    * GUI
    * Maps
    * Prefabs
    * Sounds

  * Script:
    * ThridPart
    * Utils
    * Managers
    * AssetBundle
    * Exceldecoder
  
  * Editor:
    * AssetBundle
    * Common 
    * ExcelBuild
    * GUIBuild
    * PackageBuild

  * ...

## Prefabs
可将多个模型组成一个Prefab，组成各种模型组合Prefab，Prefab也可嵌套入Prefab。
<br>![image](https://user-images.githubusercontent.com/74708198/221221227-a66bb9ba-a4de-4522-b547-e5f5318e5554.png)
<br>![image](https://user-images.githubusercontent.com/74708198/221223829-e331a65d-b40b-4238-9309-243c51a43951.png)

* Open: 可编辑模式
* Select：word means
* Overrrides: 
  * Revert All 全恢复初始
  * Apply All 将原始Prefab全替换
<br>![image](https://user-images.githubusercontent.com/74708198/221227184-34199e12-9b6e-4b3f-99ef-f54b3c9c5f9a.png)

* 创建Prefab Variant（变体）, 改变主体影响变体属性，但改变变体主题不变。
* 右键Prefab Unpack可以将其转化为GameObject。
<br>![image](https://user-images.githubusercontent.com/74708198/221229033-8a52a3bb-2ea1-4a8a-be87-35a2c53fdc32.png)
* File > Build Seting > Player Setting: 进入编辑模式后创建新的编辑场景，可以用于画风对比，测量尺寸等等。
