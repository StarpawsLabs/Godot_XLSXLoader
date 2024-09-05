# Godot_XLSXLoader
godot XLSX表格文件快读加载器，可以快速加载配置。

下面是项目的示例地址<https://github.com/seeaworld/XLSX_Loader_Example>

插件使用的环境为Godot4.3
## 下面是使用方法
1. 导入插件
    * 将项目下载并将其中的addon文件直接复制到你的项目的根目录    
    <img src="Pic/import_addon_2.png">
    * 在你的项目中启用插件，***每次导入一个EXCEL重启一次编辑器*** 
    <img src="Pic/open_addon.png">

    <img src="Pic/restart.png">
    * 重启完编辑器后，记得将"res://addons/XLSXLoader/init_xlsx_load.gd"文件作为预加载加入你的游戏。一定要记得加入，否则导出会出错。
    <img src="Pic/open_auto_start.png">
    * 现在你就可以使用了。详见下文

2. 在编辑器中编辑
   * 在你需要放置xlsx文件的地方右键，在文件管理器中打开
      <img src="Pic/explorer.png">
   * 然后再文件资源管理器中添加xlsx文件
   <img src="Pic/create_xlsx.png">
   * 创建文件后，回到godot编辑器中。右键，使用外部编辑器打开。
   <img src="Pic/open_e.png">
   * 然后编辑。Godot_XLSXLoader的规则详见下文。
   
3. XLSX文件编写方法
   * XLSX文件的第一行是你的标题，比如姓名，血量，攻击。
   * XLSX文件的第一列是数据的键。当你的数据需要索引时你需要将此数据放在第一列，比如姓名，ID
    <img src="Pic/use_xy.png">
   * 插件仅仅支持字符串，浮点，枚举的储存，请不要写别的类型。如果你有别的数据类型需要导入，可以告诉作者，作者试试看
  
        <img src="Pic/show_table_1.png" >
4. 游戏内使用方法
   * 举个例子来表示如何使用：
    比如我再xlsx文件中写入了如下内容
    <img src="Pic/xlsx_show_all.png" >
   * 插件将xlsx文件以 EXCEL 类的方式导入，你需要以EXCEL 类的方式导入及使用
    ```
    @export var data:EXCEL
    ```
    或者使用load
    ```
    func _ready():
	    var data=load("res://data/xlsxexp.xlsx")
    ```
   * 然后使用方法读取
    ```
    data.get_data("表格1")
    ```
## 以下是示例代码：
```
@export var data:EXCEL
func _ready():
print(data.get_data("表格1"))
```
   * 现在已经完成了文件导入，将你的xlsx文件拖动到检查器就可以直接开始读取了。运行游戏，你会看到如下打印：
    <img src="Pic/show_dic.png">
    * 是的，读取出来就是一个字典。接下来示例代码将会告诉你如何使用这个字典。
  
        ### 比如，我有一些敌人的数据，需要通过这个字典转换为Resource放在data文件夹。
        表格如下：
        <img src="Pic/xlsx_show_all.png">
        在主场景 main中写如下：
        ```
        #main.gd
        extends Control

        @export var data:EXCEL
        func _ready():
	        var character_datas=data.get_data("character")

	        for character_data in character_datas:

		        var character_res=character.new(
                    character_datas[character_data]["姓名"],
                    character_datas[character_data]["血量"],
                    character_datas[character_data]["攻击"],
                    character_datas[character_data]["敌友"]
                    )

		        ResourceSaver.save(character_res,"res://exp1/data/"+character_res.姓名+".tres")

        ```
        其中，get_data的"character"为表格名（不是文件名）
        遍历字典并将每一个数据写入到resource中，并保存到文件里。


        resource资源代码如下：
        ```
        #character.gd
        extends Resource
        class_name character
        @export var 姓名:String
        @export var 血量:int
        @export var 攻击:float
        @export var 敌友:是敌是友

        func _init(_姓名:String,_血量:int,_攻击:float,_敌友:String) -> void:
	        姓名=_姓名
	        血量=_血量
	        攻击=_攻击
	        var keys:Array=是敌是友.keys()
	        var values:Array=是敌是友.values()
	        敌友=values[keys.find(_敌友)]
        enum 是敌是友
        {
	        敌,
	        友
        }
        ```
        
        其中，"姓名"属性为字符串，直接从string中初始化即可。
        
        "血量"与"攻击"是数字，也是直接使用即可。
        
        但是"敌友"是枚举，需要一些小小的操作，从string导入，然后用枚举相关方法导入为枚举。
        ```
	    var keys:Array=是敌是友.keys()
	    var values:Array=是敌是友.values()
	    敌友=values[keys.find(_敌友)]
        ```
        现在，运行场景并设置主场景，你就会看到"res://exp1/data/"目录下出现刚刚写的各种Resource信息

    