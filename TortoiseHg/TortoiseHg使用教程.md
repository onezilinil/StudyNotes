# TortoiseHg的基本使用

 **1.TortoiseHg是什么**

是分布式的源码管理工具Mercurial的GUI客户端。 mercurial 作为3大主流的分布式源码管理工具，已经被广泛的使用。例如 googlecode.com和codeplex.com都支持mercurial作为源码管理工具。下面结合某个场景，叙述TortoiseHg的具体使用方法。

**2.TortoiseHg的下载**

[Mercurial 客户端](https://www.cr173.com/soft/48257.html)

**3.基本概念**

【Init】是指初始化,也就是创建一个空的仓库。 

【Clone】是指创建仓库的复本,创建的的来源可以是一个http或ssh链接表示的仓库,也可以是本地仓库。 

【Commi】t是指提交，即接受所有的改动，将当前版本作为最新版本（也叫tip）。注意这个提交和集中式SCM不同，它只提交本地仓库，而且本地仓库会详细记录所有的变动（陈伟变更集或改动集）。因此多提交几次没有关系，不影响别人的仓库。如果怕影响自己的话，可以克隆一个副本，再对副本进行改写和提交。

【Revert】是指恢复，即撤销所有的改动，恢复到未更改的状态。

【Update】意思是更新，当获取了其他变更集，且需要更改以保持和其他仓库一致时，可以执行更新操作。

【Pull】是指拉取，将远端仓库有而当前仓库没有的变更集“下载”到本地仓库。不过这只是添加了变更集，本地文件并不会改动。

【Push】是指推送，将当前仓库的变更集“上传”到其他仓库去。其他的仓库必须支持该操作，且可能需要经过验证。如果从本地仓库A push到本地仓库B，相当于用B pull A，push也不会更新仓库的版本，因为它是共享的，需要的人直接选择想要的版本或者进行合并操作即可。

【Merge】是指合并，当一个文件同时被多次修改，并存放在多个仓库时，要获得一个统一的版本就需要合并。一个文本可能同时分给多个人，于是合并时只需要接受每个人的改动即可。

**4.TortoiseHg的基本使用**

安装完成后右键菜单中出现如下的图片就可以正常使用了

![安装tortoisehg成功](https://allenwan14353.github.io/MyPostImages/TortoiseHg/安装tortoisehg成功.png)

* 创建仓库和克隆仓库

  * 创建仓库

  ​	   `右键点击文件夹 -> TortoiseHg -> create repository here`

![创建仓库和克隆仓库操作-创建仓库.png](https://allenwan14353.github.io/MyPostImages/TortoiseHg/创建仓库和克隆仓库操作-创建仓库.png)

  * **注意：** 这里新建好的仓库会自动生成`.hg`文件夹和`.hgignore`文件，其中`.hgignore`文件是未被跟踪的，未被跟踪的文件是不能提交、拉取和上传的，就相当于这个文件只是一个放在这个文件夹下的文件，是没有被TortoiseHg管理，不能同步的文件。
  
  * 可以将文件都被追踪标记，让父仓库中所有的文件都可以被克隆。
  
    `右键需要被追踪标记的文件->commit `
  
    ![创建仓库和克隆仓库操作-add和commit文件.png](https://allenwan14353.github.io/MyPostImages/TortoiseHg/创建仓库和克隆仓库操作-add和commit文件.png)
  
  * 克隆仓库
  
    1.需要先打开父仓库的server服务
  
    `右键点击文件夹 -> TortoiseHg -> web server`
  
    ![创建仓库和克隆仓库操作-webserver服务按钮.png](https://allenwan14353.github.io/MyPostImages/TortoiseHg/创建仓库和克隆仓库操作-webserver服务按钮.png)
  
     2.先进行一些基本的设置
  
    点击设置按钮（也可以`右键文件夹->tortoisehg->global setting`），设置`提交`中的用户名（提交代码的时候可以显示是谁提交的，不设置的话会在提交的时候报错）和`Server`（没有密码就不要使用ssl加密推送，否则push的时候会报错；允许推送改为 * ，向所有用户名推送；设置编码格式utf-8）
  
    ![创建仓库和克隆仓库操作-webserver设置1.png](https://allenwan14353.github.io/MyPostImages/TortoiseHg/创建仓库和克隆仓库操作-webserver设置1.png)
  
    ![创建仓库和克隆仓库操作-webserver设置2.png](https://allenwan14353.github.io/MyPostImages/TortoiseHg/创建仓库和克隆仓库操作-webserver设置2.png)
  
    3.开启服务器（如果点启动开启不了服务器，说明端口被占用了，换一个就可以。这里路径和本地路径的区别：路径是指开启服务器后的会添加在url中的地址，比如设置路径为`/hg1`，则克隆的时候url地址为`http://localhost:8000/hg1`;本地地址则是url地址映射的本地文件。）
  
    ![创建仓库和克隆仓库操作-开启webserver服务.png](https://allenwan14353.github.io/MyPostImages/TortoiseHg/创建仓库和克隆仓库操作-开启webserver服务.png)
  
    4.克隆父仓库
  
    `右键点击文件夹 -> TortoiseHg -> clone`
  
    ![创建仓库和克隆仓库操作-克隆仓库按钮.png](https://AllenWan14353.github.io/MyPostImages/TortoiseHg/创建仓库和克隆仓库操作-克隆仓库按钮.png)
  
    `输入服务器地址+路径`
  
    ![创建仓库和克隆仓库操作-克隆仓库设置.png](https://AllenWan14353.github.io/MyPostImages/TortoiseHg/创建仓库和克隆仓库操作-克隆仓库设置.png)
  
    5.克隆好的文件夹如下，hg1 是远端仓库，hg2 和 hg3 是克隆的本地仓库：

  ![创建仓库和克隆仓库操作-创建三个仓库.png](https://AllenWan14353.github.io/MyPostImages/TortoiseHg/创建仓库和克隆仓库操作-创建三个仓库.png)

  这里我的电脑不会显示已经被tortoisehg管理的仓库的同步标记（如下图一样的标记），我感觉是有可能我电脑此前装过SVN，有冲突导致不会显示同步文件的标记，具体地情况在网上也没有找到很好的解决方法，希望有大佬可以进行解答。

  ![文件没有同步的标记.png](https://AllenWan14353.github.io/MyPostImages/TortoiseHg/文件没有同步的标记.png)

* 文件pull（下拉）和push（推送）

  * 文件推送

    将本地仓库中的文件推送到远端仓库，这里测试一下只`commit不push`和`commit并push`的区别。

    * 在hg2仓库中创建一个`hg2.txt`文件，创建完毕后add并<font color="red">commit不push</font>一下。

      ![拉取和提交操作-commit不push.png](https://AllenWan14353.github.io/MyPostImages/TortoiseHg/拉取和提交操作-commit不push.png)

    * 在hg3仓库中创建一个`hg3.txt`文件，创建完毕后add并<font color="red">commit并push</font>一下。

      ![拉取和提交操作-commit并push.png](https://AllenWan14353.github.io/MyPostImages/TortoiseHg/拉取和提交操作-commit并push.png)

    * 在操作完成后可以查看远端仓库hg1workbench版本状态的变化，可以看见当前仓库有了一个新的版本，是hg3仓库推送上来的文件，而hg2仓库提交的文件还在本地仓库，hg1仓库可以通过`pull`获取到hg2仓库的文件。

      ![拉取和提交操作-远端仓库workbench状态.png](https://AllenWan14353.github.io/MyPostImages/TortoiseHg/拉取和提交操作-远端仓库workbench状态.png)

    * 这时候远端仓库hg1可以对上传上来的文件进行`update`（更新，这里的更新会采取选中的版本库覆盖原本的库）或者`merge with local`（和本地文件合并，会将选中的版本库和本地的版本库合并，若本地的版本库中，没有选中的push上来的版本库的文件，运行合并操作会报错，需要进行`update`）操作。

      ![拉取和提交操作-合并push上来的文件.png](https://AllenWan14353.github.io/MyPostImages/TortoiseHg/拉取和提交操作-合并push上来的文件.png)

  * 文件下拉

    将远端仓库中的文件下拉到本地仓库，这里测试hg2仓库从hg1仓库下拉获取到hg3.txt文件。

    * 这里hg1仓库已经`update`了hg3仓库`push`的文件，可以通过点击同步按钮，修改拉取的目标仓库`同步->选择目标仓库->下拉`拉取。

      ![拉取和提交操作-pull远端仓库文件.png](https://AllenWan14353.github.io/MyPostImages/TortoiseHg/拉取和提交操作-pull远端仓库文件.png)

    * 拉取后仓库会变成本地仓库的一个版本，而且一个分支，但是现在本地文件还没有改变，可以右键选中分支，进行更新或者合并操作，更新操作会覆盖掉本地相同的文件，但是会多出一个.orig的文件，这就是原始版本的文件，可以点击原始的分支进行还原，合并操作的时候，如果同时改动同一个文件的同一个地方，并且实现没有进行pull同步操作，则会造成冲突。

      这里测试，同时在hg1和hg2仓库对hg3.txt文件进行修改并提交，将hg3.txt文件下拉到hg2仓库，并进行合并操作。

      ![拉取和提交操作-冲突产生.png](https://AllenWan14353.github.io/MyPostImages/TortoiseHg/拉取和提交操作-冲突产生.png)

    * 解决冲突，点击`tool merge`，<font color="red">使用KDiff3 工具</font>解决冲突使用方法，主要是在output下面的框操作冲突的代码，使用快捷键 ctrl + 数字（1或2或3）获取冲突的文件的内容m，也可以使用工具栏中的选项进行冲突代码的选择，（上面的工具栏基本没有上面用，只是可读，不可写，下面的文本框才可写的），然后设置编码 encoding for saving ,设置line end style，和冲突文件的编码一致就可以，解决完后就可以保存了。
    
      ![拉取和提交操作-冲突解决1.png](https://AllenWan14353.github.io/MyPostImages/TortoiseHg/拉取和提交操作-冲突解决1.png)
    
      ![拉取和提交操作-冲突解决2.png](https://AllenWan14353.github.io/MyPostImages/TortoiseHg/拉取和提交操作-冲突解决2.png)

[TortoiseHg 学习笔记](https://blog.csdn.net/xukai871105/article/details/25649331)

[Mercurial与TortoiseHg使用入门教程](https://wenku.baidu.com/view/c69e9d34a32d7375a41780c4.html?rec_flag=default&sxts=1563428054883)