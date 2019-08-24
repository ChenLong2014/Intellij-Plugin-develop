This is Intellij plugin develop Read.me.

相较于其他的Java web 的开发来说，Intellij 的插件开发方式在思路上是有所不同的。因此，在开发的时候，需要理解插件开发的方式，Intellij 在开发时候的思路。下面介绍一下整体的开发方式。

1. Intellij 在写文件的时候，必须放入到指定的application.runWriteAction 中。
	Intellij 有自己的文件系统，VirtualFile 与 PsiDirectory, VirtualFile 可以用来循环判断文件与子文件，与java.io.File 比较相同，可以使用inputStream 和 outputStream 来输入和输出。
	使用PsiDirectory  可以创建文件夹与文件。
	此线程是单线程的工作机制，因此，当我们在写入的时候，会出现页面卡死的情况。
	在Intellij 中，如果需要使用写入的功能时，要么通过原始的java.io.File 的writer 来写入。
	要么通过VirtualFile 来写入。两者之间的区别在于，通过VirtualFile 与 PsiDirectory 的方式，能够直接的表现在Intellij 上面。而通过java.io.File 需要过一定的时间才能反映出来。效果会打打折扣。
	这样子做的好处是插件开发的可控性。


2. Intellij 想要去异步的做一些事情，可以使用application.runReadAction
	        ApplicationManager.getApplication().executeOnPooledThread(new Runnable() {
            public void run() {
                ApplicationManager.getApplication().runReadAction(new Runnable() {
                    public void run() {
                       // TODO
                    }
                });
	单纯的异步动作，不需要在Intellij 本身的project 做修改。

3. 当Intellij 一打开的时候，去监听这个动作？
	 <project-components>
		<component>
		  <implementation-class>com.huawei.component.InitApplicationComponent</implementation-class>
		</component>
	 </project-components>
  
	public class InitApplicationComponent implements ApplicationComponent
	{
		@Override
		public void initComponent() {
		 }

	}

4. 添加一个类似Structure 的tool-window
	<extensions defaultExtensionNs="com.intellij">
    <!-- Add your extensions here -->
      <toolWindow id="ModelArts Explorer" secondary="true" anchor="left" factoryClass="com.huawei.ui.ExplorerToolWindowFactory"/>
	</extensions>
	
	
	
	
	
	