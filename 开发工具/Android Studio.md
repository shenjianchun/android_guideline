# 1. Android Studio快捷键/小技巧

## 1.1 常用技巧

1. 折叠/展开代码块（Collapse Expand Code Block）

   * **描述：**该操作提供一种方法，让你隐藏你不关心的部分代码，以一种较为简洁的格式显示关键代码。一个有意思的用法是隐藏匿名内部类的代码，让其看起来像一个Lambda表达式。
   * **快捷键：** Cmd + “+/-“(OS X)、Ctrl + Shift + “+/-“(Windows/Linux);
   * **更多：**可以在Setting → Editor → General → Code Folding 中设置折叠规则。

   ![img](https://jaeger.itscoder.com/assets/img/studio_tips/06-codefolding.gif)



2. 上下文信息（Context Info）

   - **描述：**当前作用域定义超过滚动区域，执行该操作将显示所在的上下文信息，通常它显示的是类名或者内部类类名或者当前所在的方法名。该操作在xml文件中同样适用。
   - **调用：**Menu → View → Context Info
   - **快捷键：**Alt + Q (Windows/Linux)
   - **更多：**个人认为，这个功能更好的用法是快速查看当前类继承的父类或者实现的接口。

   ![img](https://jaeger.itscoder.com/assets/img/studio_tips/47-contextinfo.gif)



3.  查找操作（Find Action）

   - **描述：**输入某个操作的名称，快速查找，对于没有快捷键的部分操作这是一个很有用的技巧。
   - **快捷键：**Cmd +Shift + A(OS X)、Ctrl + Shift + A(Windows/Linux)；
   - **更多：**当某个操作是有快捷键的，会显示在旁边。

   ![img](https://jaeger.itscoder.com/assets/img/studio_tips/08-findaction.gif)



4. 隐藏所有面板（Hide All Panels）

   - **描述：**切换编辑器铺满整个程序界面，隐藏其他的面板。再次执行该操作，将会回到隐藏前的状态。
   - **调用：**Menu → Window → Active Tool Window → Hide All Windows；
   - **快捷键：**Cmd +Shift + F12(OS X)、Ctrl + Shift + F12(Windows/Linux)；

   ![img](https://jaeger.itscoder.com/assets/img/studio_tips/42-hideallwindows.gif)

   

5. 上一个编辑位置（Last Edit Location）

   - **描述：**该操作将使得你导航到上一处你改动过的地方，这与点击工具栏上的返回箭头回到上一个定位位置是不一样的，该操作将会返回到上一个编辑的位置。
   - **快捷键：** Cmd + Shift + Delete(OS X)、Ctrl + Shift + Backspace﻿(Windows/Linux);

   ![img](https://jaeger.itscoder.com/assets/img/studio_tips/17-navigate-previous-changes.gif)

   

6. 在方法和内部类之间跳转（Move Between Methods and Inner Classes）

   - **描述：**该操作让光标在当前文件的方法或内部类的名字间跳转。
   - **调用：**Navigate → Next Method/Previous Method;
   - **快捷键：**Ctrl + Up/Down﻿(OS X)、Alt + Up/Down﻿(Windows/Linux);

   ![img](https://jaeger.itscoder.com/assets/img/studio_tips/02-move_between_methods.gif)

   

7. 定位到嵌套文件（Navigate to Nested File）

   - **描述：**有时你有一堆存放在不同目录下的同名文件，例如不同模块下的`AndroidManifest.xml`文件，当你想定位到其中的一个文件，你会得到一堆搜索结果，你还得辨认哪个才是你需要的。通过在检索框中输入部分路径的前缀，并添加斜杠号，你就可以在第一次尝试的时候就找到正确的那个。
   - **快捷键：**Shift + Cmd + O(OS X)、Shift + Ctrl + N﻿(Windows/Linux);

   ![img](https://jaeger.itscoder.com/assets/img/studio_tips/63-nestednavigation.gif)



8. 定位到父类（Navigate to parent）

   - **描述：**如果光标是在一个继承父类重写的方法里，这个操作将定位到父类实现的地方。如果光标是在类名上，则定位到父类类名。
   - Menu → Navigate → Super Class/Method
   - **快捷键：**Cmd + U(OS X)、Ctrl + U(Windows/Linux);

   ![img](https://jaeger.itscoder.com/assets/img/studio_tips/39-navigatetoparent.gif)



9. 在外部打开文件（Open File Externally）

   - **描述：**通过这个快捷键，简单地点击 Tab，就可以打开当前文件所在的位置或者该文件的任意上层路径。
   - **快捷键：**Cmd + 单击Tab(OS X)、Ctrl + 点击Tab(Windows/Linux);

   ![img](https://jaeger.itscoder.com/assets/img/studio_tips/62-openfinder.gif)





10. 快速查看定义（Quick Definition Lookup）

    - **描述：**你曾经是否想查看一个方法或者类的具体实现，但是不想离开当前界面？ 该操作可以帮你搞定。
    - **快捷键：**Alt + Space / Cmd + Y(OS X)、Ctrl + Shift + I(Windows/Linux)

    ![img](https://jaeger.itscoder.com/assets/img/studio_tips/05-quickdefinition.gif)







11. 最近访问（Recents）

    - **描述：**该操作可以得到一个最近访问文件的可搜索的列表。
    - **快捷键：**Cmd + E(OS X)、Ctrl + E(Windows/Linux)

    ![img](https://jaeger.itscoder.com/assets/img/studio_tips/14-recents.gif)

    

12. 最近修改的文件（Recently Changed Files）

    - **描述：**该操作类似于“最近访问（Recents）”弹窗，会显示最近本地修改过的文件列表，根据修改时间排列。可以输入字符来过滤列表结果。
    - **快捷键：**Cmd + Shift + E(OS X)、Ctrl + Shift + E(Windows/Linux)

    ![img](https://jaeger.itscoder.com/assets/img/studio_tips/49-recentlyedited.gif)



13. 相关文件（Related File）

    - **描述：**该操作有助于在布局文件和Activity/Fragment之间轻松跳转。这也是一个快捷操作，在类名/布局顶端的左侧。
    - **快捷键：**Ctrl + Cmd + Up(OS X)、Ctrl + Alt + Home(Windows/Linux)

    ![img](https://jaeger.itscoder.com/assets/img/studio_tips/50-relatedfile.gif)



14. 返回到编辑器（Return to the Editor）

    - 描述：

      一大堆快捷键操作会把你从编辑器带走（type hierarchy, find usages, 等等）。如果你想返回到编辑器，你有两个选项：

      1. Esc：该操作仅仅把光标移回编辑器。
      2. Shift + Esc：该操作会关闭当前面板，然后把光标移回到编辑器。

    - 快捷键：

      - 返回但保留打开的面板：Esc
      - 关闭面板并返回：Shift + Esc

    ![img](https://jaeger.itscoder.com/assets/img/studio_tips/40-returntoeditor.gif)

    

15.  扩大/缩小选择（Extend/Shrink Selection）

    - **描述：**该操作会在上下文逐渐扩大/缩小当前选择范围。例如，它会先选中当前变量，再选中当前语句，然后选中整个方法，缩小选择则相反。
    - **快捷键：**Alt + 上/下 (OS X)、Ctrl+W / Ctrl + Shift + W﻿（Windows、Linux）

    ![img](https://jaeger.itscoder.com/assets/img/studio_tips/12-expand_shrink_selection.gif)

16. Sublime Text式的多处选择（Sublime Text Multi Selection）

    - **描述：**这个功能超级赞！该操作会识别当前选中字符串，选择下一个同样的字符串，并且添加一个光标。这意味着你可以在同一个文件里拥有多个光标，你可以同时在所有光标处输入任何东西。
    - **快捷键：**Ctrl + G(OS X)、Alt + Ｊ（Windows、Linux）

    ![img](https://jaeger.itscoder.com/assets/img/studio_tips/32-multiselection.gif)

17. 文件结构弹窗（The File Structure Popup）

    - **描述：**该操作可以展示当前类的大纲，并且可以快速跳转。你还可以通过键盘输入来过滤结果。这是一种很高效的方法来跳转到指定方法。
    - 更多：
      - 你在输入字符的时候可以用驼峰风格来过滤选项。比如输入”oCr”会找到”onCreate”
      - 你可以通过勾选多选框来决定是否显示匿名类。这在某些情况下很有用，比如你想直接跳转到一个OnClickListener的onClick方法。
    - **快捷键：**Cmd + F12(OS X)、Ctrl + F12(Windows/Linux)
    - **调用：**Menu → Navigate → File Structure

    ![img](https://jaeger.itscoder.com/assets/img/studio_tips/03-filestructure.gif)





## 1.2 编码技巧





## 参考

- 官网快捷键：https://developer.android.com/studio/intro/keyboard-shortcuts.html
- 快捷键总结：https://jaeger.itscoder.com/android/2016/02/14/android-studio-tips.html

* Android Studio Tips
  * http://www.developerphil.com/android-studio-tips-of-the-day-roundup-1/
  * http://www.developerphil.com/android-studio-tips-of-the-day-roundup-2/
  * http://www.developerphil.com/android-studio-tips-of-the-day-roundup-3/
  * http://www.developerphil.com/android-studio-tips-of-the-day-roundup-4/
  * http://www.developerphil.com/android-studio-tips-of-the-day-roundup-5/
  * http://www.developerphil.com/android-studio-tips-of-the-day-roundup-6/







# 2. Android Studio插件

1. ADB Idea ,  一键清理缓存、卸载，重启 APP
2. Codota  ， 查找代码示例
3. GsonFormat ， 通过Gson 字符串自动生成Bean文件。
4. android-parcelable-intellij-plugin     ，自动实现Parceable接口序列化
5. FindBugs  ,  查找代码中可能存在的bug
6. Lifecycle Sorter,  按照Activity和Fragment的生命周期来排列方法
7. SelectorChapek, 通过资源文件名自动生成Selector Drawable
8. Android Methods Count ， 显示依赖库中的方法数
9. Android Code Generator     , 根据布局文件快速生成对应的Activity，Fragment，Adapter，Menu