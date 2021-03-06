### 目录（开发平台后端框架参考指南流程引擎子模块，序号延续母文档）

- [3.3 工作流组件](#bpm)
    - [3.3.1 流程引擎基本信息](#bpm_1)
    - [3.3.2 集成及获取服务](#bpm_2)
    - [3.3.3 第一个流程](#bpm_3)
        - [在管理页面设计流程图（包括流程参与人、操作等的配置）](#bpm_3_1)
        - [在开发项目中引入bpm模块](#bpm_3_2)
        - [项目中的流程配置](#bpm_3_3)
        - [流程发起及操作](#bpm_3_4)
    - [3.3.4 开发约定](#bpm_4)
    - [3.3.5 流程服务说明](#bpm_5)
        - [流程服务总述](#lcfwzs)
        - [【示例】application中配置流程信息](#properties)
        - [【示例】会签使用场景](#huiqian)
    - [3.3.6 添加自定义逻辑](#bpm_6)
    - [3.3.7 SDK接口](#bpm_7)
        - [说明](#bpm_7_1)
        - [获取接口的方式](#bpm_7_2)
        - [接口列表](#bpm_7_3)
        - [Model说明](#bpm_7_4)
    - [3.3.8 异常说明](#bpm_8)
    - [3.3.9 同步模块说明](#bpm_9)
    - [3.3.10 接口使用统计](#bpm_10)
    - [3.3.11 版本变更历史](#bpm_11)
    - [3.3.12 工作流平台架构图](#bpm_12)
    - [附注](#fz)

### <span id="bpm">3.3 工作流组件</span>

#### <span id="bpm_1">3.3.1 流程引擎基本信息</span>

- 术语

> - 流程定义：业务过程的形式化描述，过程可分解为一系列的子过程和活动，其中定义包括描述过程起始、终止的活动关系网络，以及一些关于个体行为的信息，具体而言，即构成过程的活动以及各活动的关系、组织成员的角色、应用中的数据结构等。
> - 活动（activity）：流程定义中的活动，定义了在业务过程中某个环节的信息
> - 流程实例：一个工作流过程的具体执行。
> - 任务：对应流程定义中的活动，是具体的执行实例
> - BPM：业务流程管理(Business Process Management)
> - BPM模块：开发平台建设的流程相关子项目模块，项目名“chamc-boot-starter-bpm”

- 介绍

>流程引擎模块属于开发平台的一个子模块，专门为业务系统提供工作流及业务自动化服务。该模块能够以maven依赖的方式快速引入在建项目中，对于基础的流程发起、待办已办任务查询等操作，直接调用相应的rest接口即可。在提供流程处理基础服务 + 租户、角色等基础管理服务的基础上，项目支持在管理页面设计流程图、在管理后台操作流程。希望通过流程引擎模块，为应用及项目的开发提供支持。

- SDK接口说明

>SDK组件封装了流程引擎的服务，以方便各个系统的个性化需求。

#### <span id="bpm_2">3.3.2 集成及获取服务</span>

3.3.2.1 集成方式

流程引擎以jar工具包的形式提供服务：

- maven项目的集成：  

**Step 1**：配置/检查私服配置

参考[2.3.4获取组件](#get-weg)，检查/配置maven的私服配置信息

**Step 3**：项目依赖添加

在pom文件的`<dependencies></dependencies>`中添加信息，如下所述，groupId为`com.chamc.boot`.

	<dependency>
		<groupId>com.chamc.boot</groupId>
		<artifactId>chamc-boot-starter-bpm</artifactId>
		<version>0.0.1-SNAPSHOT</version>
	</dependency>

**Step 4**：在工程目录上右键，选择 `Maven` -> `Update Project` -> `OK`，项目自动编译后，如果没有异常，就能够使用流程服务了。

#### <span id="bpm_3">3.3.3 第一个流程</span>

- 步骤概述：

> [Step 1：在管理页面设计流程图（包括流程参与人、操作等的配置）](#bpm_3_1)  
> [Step 2：在开发项目中引入`starter-bpm`模块](#bpm_3_2)  
> [Step 3：项目中的流程配置](#bpm_3_3)  
> [Step 4：流程发起及操作](#bpm_3_4)

- Step 1：<span id="bpm_3_1">在管理页面设计流程图（包括流程参与人、操作等的配置）</span>

1）访问并登录**流程引擎后台管理平台**，登录后主页面及导航栏如图process-1所示，`系统设置`的子菜单可以管理用户、机构和角色等，可以分别点击进行对应的操作。为了方便使用，系统初始已预置初始数据，可查看确认。

![图 process-1：系统设置](https://i.imgur.com/ls4so0u.jpg)

<center>图 process-1：系统设置</center>

2）点击`工作流管理`-`流程创建管理`，如图process-2所示，进入流程设计管理页面，在该页面可以完成流程的新建、发布等。

![图 process-2：流程设计界面](https://i.imgur.com/2iqIYgv.png)

<center>图 process-2：流程设计界面</center>

3）点击新建流程，如图process-3所示，填写流程定义信息（所属租户，流程key等），填写完成后，点击确认保存流程定义信息。再次点击主页面的导航栏`工作流管理`-`流程创建管理`,可以看到列表第一条数据为刚才新建的流程，点击编辑操作，进入流程设计界面，如图process-4所示。

![图 process-3：新建流程](https://i.imgur.com/WoDqP2d.png)

<center>图 process-3：新建流程</center>

![图 process-4：进入流程设计界面](https://i.imgur.com/2xZUH6z.png)

<center>图 process-4：进入流程设计界面</center>

4）设计流程图，在demo示例中设计简单的流程（启动-制单-领导审批-结束）。点击左侧`启动事件`，将`事件`控件拖入页面中间设计区域，如图process-5所示；点击拖入的`事件`，周围显示可以连接的其他控件，点击`分配给特定人的任务`，如图process-6所示，添加一个新的activity；按照同样的步骤，点击刚新建的activity，在周围出现的选项中点击`分配给特定人的任务`，添加第二个activity；同理点击第二个activity，选择红色的圈（一个无触发器的结束任务），添加结束节点。

![图 process-5：拖入启动事件](https://i.imgur.com/5Nn8DQ3.png)

<center>图 process-5：拖入启动事件</center>

![图 process-6：添加activity](https://i.imgur.com/Qlx6Iyv.png)

<center>图 process-6：添加activity</center>

5）配置activity。如图process-7所示，点击后可以在右侧菜单设置activity属性，点击创建的第一个activity，输入id、名称，点击代理后的输入框，选择参与人信息，如图process-8所示，确认后点击任务节点功能，配置对于该activity的功能操作，勾选下一步操作，保存确认后，点击流程设计页面中控件栏上方的保存按钮，并关闭该页面，如图process-9所示。

![图 process-7：修改属性](https://i.imgur.com/Ixi3we6.png)

<center>图 process-7：修改属性</center>

![图 process-8：设置参与人](https://i.imgur.com/3iyabyT.png)

<center>图 process-8：设置参与人</center>

![图 process-9：保存](https://i.imgur.com/91F6Xfp.png)

<center>图 process-9：保存</center>

6）发布流程图，再次点击主页面的导航栏`工作流管理`-`流程创建管理`，找到刚编辑的流程定义的条目并点击发布，提示流程发布成功，即完成了流程的设计与发布过程。

- Step 2：<span id="bpm_3_2">在开发项目中引入`bpm`模块</span>

1）：集成方式

参考[开发平台后端框架环境搭建](http://hq-spsdocument/_layouts/15/DocIdRedir.aspx?ID=C2A742TNNUZA-1797567310-1213)给开发环境配置私服；

2）：项目依赖添加

根据第一步下载的文件夹中的pom文件描述，可以获取到依赖包的artifactId和version，在pom文件的`<dependencies></dependencies>`中添加信息，如下所述，groupId为`com.chamc.boot`，版本信息可通过私服查看最新版本；

	<dependency>
		<groupId>com.chamc.boot</groupId>
		<artifactId>chamc-boot-starter-bpm</artifactId>
		<version>0.0.1-SNAPSHOT</version>
	</dependency>

3）：在工程目录上右键，选择 `Maven` -> `Update Project` -> `OK`，项目自动编译后，如果没有异常，就能够使用流程服务了。


- Step 3：<span id="bpm_3_3">项目中的流程配置</span>

以SpringBoot项目为例：

1）在配置文件（application.properties）中添加如下所示的内容：

    chamc.bpm.type=tansun
    chamc.bpm.tansun.url=http://localhost:8080/workflow-console #流程引擎服务地址
    chamc.bpm.tansun.tenant-id=archive  #租户ID，可以在“流程引擎后台管理平台——工作流管理——租户管理”查看
    chamc.bpm.tansun.default-classify-code=root #流程分配编码

- Step 4：<span id="bpm_3_4">流程发起及操作</span>

1）发起流程

在SpringBoot项目需要的地方注入`com.chamc.boot.bpm.service.IInstanceService`，调用iInstanceService.startBpm(startBpmParam)即可启动流程，示例：

    import org.springframework.beans.factory.annotation.Autowired;

    import com.chamc.boot.bpm.model.process.param.StartBpmParam;
    import com.chamc.boot.bpm.service.IInstanceService;

    public class InstanceServiceDemo {

    	private @Autowired IInstanceService iInstanceService;
    
    	public void startBpm() {
    		StartBpmParam startBpmParam = new StartBpmParam();
    		startBpmParam.setProcessKey("process-helloworld");
    		startBpmParam.setUserId("user1");
    		startBpmParam.setTitle("测试任务标题");
    		iInstanceService.startBpm(startBpmParam);
    	}
    }

2）查看待办**/**已办任务

注入`com.chamc.boot.bpm.service.ITaskService`，调用iTaskService.queryTodo(userId, pageable)方法查看待办任务列表，根据[流程定义信息](#bpm_3_1)，此时第一个activity配置的参与人应收到一条待办任务。或者通过queryDone接口查看已办任务列表，如下所示：

    import org.springframework.beans.factory.annotation.Autowired;
    import org.springframework.data.domain.Page;
    import org.springframework.data.domain.PageRequest;
    import org.springframework.data.domain.Pageable;
    
    import com.chamc.boot.bpm.model.task.TaskDone;
    import com.chamc.boot.bpm.model.task.TaskTodo;
    import com.chamc.boot.bpm.service.ITaskService;
    
    public class TaskServiceDemo {
    
    	private @Autowired ITaskService iTaskService;
    	
    	public Page<TaskTodo> queryTaskTodo(String userId) {
    		Pageable pageable = new PageRequest(0, 20);		//构造pageable对象，查询第1页数据，分页大小为20
    		Page<TaskTodo> todoPage = iTaskService.queryTodo(userId, pageable);
    		return todoPage;
    	}
    	
    	public Page<TaskDone> queryTaskDone(String userId) {
    		Pageable pageable = new PageRequest(0, 20);
    		Page<TaskDone> donePage = iTaskService.queryDone(userId, pageable);
    		return donePage;
    	}
    }

待办任务TaskTodo列表如下所示，包含分页信息及待办任务列表，对每个待办任务，包含该任务当前所处的环节、基本信息、可进行的操作和流程实例信息等，其中基本信息中的`pcUrl`和`mobileUrl`分别对应pc段和移动端的任务详情页，该地址可以通过配置文件配置。获取待办列表后直接访问待办的pcUrl/mobileUrl即可进入任务的详情页面。

	{
	  "content": [
	    {
	      "activity": {
	        "first": true,
	        "id": "string",
	        "last": true,
	        "name": "string"
	      },
	      "createTime": "2017-12-18T05:23:44.730Z",
	      "dueDate": "2017-12-18T05:23:44.730Z",
	      "executionId": "string",
	      "id": "string",
	      "mobileUrl": "string",
	      "name": "string",
	      "operations": [
	        {
	          "needUserId": true,
	          "op": "同意",
	          "plural": true,
	          "text": "string",
	          "url": "string",
	          "userRange": [
	            "string"
	          ]
	        }
	      ],
	      "pcUrl": "string",
	      "processInstance": {
	        "businessKey": "string",
	        "currentActivity": {
	          "first": true,
	          "id": "string",
	          "last": true,
	          "name": "string"
	        },
	        "definitionId": "string",
	        "definitionName": "string",
	        "id": "string",
	        "startParticipant": {
	          "code": "string",
	          "id": "string",
	          "name": "string",
	          "org": "string",
	          "orgName": "string",
	          "type": "USER"
	        },
	        "title": "string"
	      }
	    }
	  ],
	  "first": true,
	  "last": true,
	  "number": 0,
	  "numberOfElements": 0,
	  "size": 0,
	  "sort": {},
	  "totalElements": 0,
	  "totalPages": 0
	}

3）处理任务

在任务详情页对待办任务进行操作，操作的按钮可以从待办任务的operations中获取，operations为对象数组，Operation返回值对象，包含信息较多：1、返回对应activity可进行的操作、操作名称及url；2、通过isNeedUserId标识该操作是否需要传用户id；3、通过isPlural标识需要用户id的话，是传1个还是多个；4、如果传的用户id（即下一个activity要用的用户id）有限定范围，那么会通过userRange返回给用户。operation对象示例可参考[Operation](#Operation)。

对于前端使用来说，点击按钮时，取Operation中的url值，并按照Operation的要求附加参数，使用示例如下所示。

如果需要在处理任务之前或者之后添加业务逻辑，可参考[3.3.6 添加自定义逻辑](#bpm_6) 内容说明，通过添加`Listener`即可实现。

<span id="actionTask">前端任务操作示例：actionTask</span>

	function actionTask(instanceId, taskId, url, needUserId, plural) {
		if(needUserId) {
			//弹框选人
		}
		var params = {};
		params.instanceId = instanceId;
		params.taskId = taskId;
		params.operatorId = "";
		params.comment = $("#task_comment").val();
		params.variables = {};
		if(needUserId) {
			//添加用户变量，多个用逗号隔开
			params.userId = "default_user_id";
		}
		$.post(url, params, function(result) {
			if("OK" == result) {
				document.location = "http://localhost:8000/zouzhentian@chamc.com.cn@8499/todo";
			} else {
				alert(result);
			}
		});
	}

4）查看流转明细

    private @Autowired IInstanceService iInstanceService;

	public List<ProcessTransferDetail> queryTransferDetail(String instanceId) {
		List<ProcessTransferDetail> result = iInstanceService.queryTransferDetail(instanceId);
		return result;
	}

至此，已经完成了第一个完整的流程。包括流程设计、流程发起、待办已办查询、任务处理及查询流转明细等。
我们把很多底层接口都已经封装到了流程引擎设计器中，所以，1.0版本对外只提供4个核心接口供业务系统，尽量减少业务系统的集成复杂度。

#### <span id="bpm_4">3.3.4 开发约定</span>

- 1、在每个任务操作后，需要提示下一步任务的参与人
- 2、部分任务需要指定下一步的参与人，需要弹窗让用户选人

#### <span id="bpm_5">3.3.5 流程服务说明</span>

1）<span id="lcfwzs">流程服务总述</span>  
流程服务模块（BPM模块）以jar包的形式添加到项目系统中，为业务系统提供流程相关的所有服务。在开发的过程中只需要按照[3.3.2 集成及获取服务](#bpm_2)为项目添加流程引擎模块依赖，并在项目配置文件中添加一些简单的信息，即可使用流程引擎服务。

BPM模块能够提供的流程引擎服务包括流程设计、流程发起、待办已办查询、任务处理及查询流转明细，并包括查询activity定义、查询流程变量和查询任务可进行的操作等。开发人员在使用流程服务时，如果需要在操作前后添加业务逻辑处理，可参考[3.3.6 添加自定义逻辑](#bpm_6)。另外对于需要接口使用的功能，注入相应的service并调用接口即可，接口的入参和出参详情可参考[3.3.7 SDK接口](#bpm_7)。

BPM模块使用前需要在项目的`application.properties`中配置流程信息，分为必须信息和可选信息两种，配置的具体信息可参考[【示例】application中配置流程信息](#properties)

如果流程中涉及到会签的使用场景，可以在设计流程图的时候进行配置，具体可参考[【示例】会签使用场景](#huiqian)

2）<span id="properties">【示例】application中配置流程信息</span>

通过配置信息，能够对指定processKey的流程的key进行配置，也能够对该key对应的每个activity的属性进行配置，包括操作按钮的名称等。

配置项以`chamc.bpm.processes`开头，后跟流程定义别名（只供配置信息用），以下示例中为`zbwsdazl`。

针对流程中单个activity的属性有`todo-pc-url、done-pc-url、todo-mobile-url、done-mobile-url、first、last`等，可以在流程设计（画流程图）的时候设置，url会在查询任务详情/任务列表时返回。

    chamc.bpm.type=tansun   #流程引擎类型
    chamc.bpm.tansun.url=http://10.1.8.117:8080/workflow-console    #流程引擎服务url
    chamc.bpm.tansun.tenant-id=archive  #租户id
    chamc.bpm.tansun.default-classify-code=root #流程分类
    
    chamc.bpm.processes.zbwsdazl.process-key=zbwsdazl_process   #以zbwsdazl为标识配置processKey为zbwsdazl_process的流程
    chamc.bpm.processes.zbwsdazl.activities.gdys.operations.agree.text=同意审批     #zbwsdazl流程中id为gdys的activity的同意按钮的名称自定义 【注：每个activity的每个按钮的名称都可以自定义，按钮类型列表参考3.3.7.x【示例】按钮列表】
    chamc.bpm.processes.zbwsdazl.activities.gdys.operations.reject.text=驳回  #id为gdys的activity的驳回按钮的名称自定义
    
    chamc.bpm.tansun.log-level=full     #可选项为NONE、BASIC、HEADERS、FULL，流程引擎的日志级别配置

3）<span id="huiqian">【示例】会签使用场景</span>

如下图所示，可以在会签属性栏配置节点的会签属性

![会签节点](https://i.imgur.com/DWonte1.jpg)

<center>会签节点</center>

注：会签节点不能被退回

> - 会签类型：并行会签/串行会签，并行时该节点所有参与人都收到待办，串行时按照入参输入的用户id次序审批；
> - 基数：可留空；
> - 集合：传入的用户集合名词
> - 元素变量：循环上述集合时，每个集合条目的循环变量名词，可用在配置参与人上；
> - 完成条件：会签结束的条件，如`${nrOfCompletedInstances/nrOfInstances >= 1}`表示任务都办理完成后结束会签

配置完上述会签属性后，可以在参与人中配置元素变量，如下所示：

![会签处理人](https://i.imgur.com/96odvJG.jpg)
<center>会签处理人</center>

#### <span id="bpm_6">3.3.6 添加自定义逻辑</span>

1）、Listener支持全局配置与局部的配置。1）、全局的listener配置对业务系统中所有的流程都生效，也就是说所有的流程只要发生操作事件就会调用的方法，比如前端通过`operation`的`url`请求操作任务时，是不带有`operator（操作人）`的参数的，但是按照逻辑来说，每个操作都需要操作人，因此可以添加全局的`listener`，并在`before`方法中给`BpmOperateParam`对象设置`operatorId`，注意：全局监听的配置，需要实现`com.chamc.boot.bpm.controller.listener.GlobalBpmOperateListener`接口，或者继承`com.chamc.boot.bpm.controller.listener.support.GlobalBpmOperateAdapter`类并按需实现方法。2）、局部的listener配置只针对特定processKey的流程，流程在流转过程中，可能需要特定的业务逻辑处理，比如对于档案系统的文书整理流程来说，发生任务操作事件时要同步的修改档案状态，这个修改逻辑只针对文书整理流程，对于其他的借阅续借流程都不需要，因此可以通过配置局部listener来为该业务添加逻辑，注意：局部监听的配置，需要实现`com.chamc.boot.bpm.controller.listener.BpmOperateListener`接口，或者继承`com.chamc.boot.bpm.controller.listener.support.BpmOperateAdapter`类并按需实现方法。

2）、listener需要注册到spring中生效，示例如下（注内容之后）（该config类要能够扫描到）：

3）、针对每个流程新建listener类并实现`com.chamc.boot.bpm.controller.listener.BpmOperateListener`（局部）接口即可，须实现该接口的`String processKey();`方法，并且在`before`方法中需要setOperatorId（操作人用户id），如下代码所示；

4）、BpmOperateListener中有`before、after、afterThrowing`三个时间段的方法，如果只需要其中一个或者几个的话，新建listener类并继承`com.chamc.boot.bpm.controller.listener.support.BpmOperateAdapter`（局部listener）即可，重载的方法同BpmOperateListener中所述方法。

5）、BpmOperateListener的before方法支持自动化的事务，后台自动判断在before方法中是否需要事务。添加listener后任务操作的执行顺序为`before -> taskOperation -> after`，如果在before中有需要事务的方法调用，那么当taskOperation）任务操作）发生异常时，会自动回滚before方法中的方法。

注：before方法中需要设置操作人ID，如`param.setOperatorId(UserUtil.getUserId());`，在任务操作时，通过待办任务返回的`operation`对象中的操作任务url是没有操作人参数的，一般来说需要后端获取登录人信息并设置属性值，因此如果是为了方便使用的话，需要一个全局的listener，用来设置操作人id，避免每个流程都需要添加局部listener并`setOperatorId`。

    import com.chamc.boot.bpm.controller.listener.BpmOperateListener;
    import com.chamc.boot.bpm.controller.param.BpmOperateParam;
    import com.chamc.boot.bpm.model.common.OperationType;
    
    public class AdminArchivingAuditListener implements BpmOperateListener {
    	
    	private final static String processKey = "zbwsdazl_process";
    
    	@Override
    	public void before(BpmOperateParam param, OperationType type) {
            //操作执行前，设置操作人
    		param.setOperatorId(UserUtil.getUserId());
            //***根据operationType的不同，添加不同的业务逻辑处理方法
    	}
    
    	@Override
    	public void after(BpmOperateParam param, OperationType type) { }
    
    	@Override
    	public void afterThrowing(BpmOperateParam param, OperationType type, Throwable t) { }
    
    	@Override
    	public String processKey() {
    		return processKey;
    	}
    }

****

    import org.springframework.context.annotation.Bean;
    import org.springframework.context.annotation.Configuration;
    
    @Configuration
    public class ListenerAutoConfig {
    
    	public @Bean AdminArchivingAuditListener adminArchivingAuditListener() {
    		return new AdminArchivingAuditListener();
    	}
    }

#### <span id="bpm_7">3.3.7 SDK接口</span>

##### <span id="bpm_7_1"> 1）说明</span>

SDK接口能够让开发人员快速的开发应用，进行灵活的流程应用的支持。在SDK中封装了工作流引擎提供的各种服务，为开发人员提供便利。

##### <span id="bpm_7_2">2）获取接口的方式</span>

目前SDK主要提供2类的接口，包路径为`com.chamc.boot.bpm.service`，接口分类列表如下：

|----|----|----|
|编号|interface|描述|
|1|IInstanceService|流程实例相关的service，包括流程启动、终止以及根据流程实例/定义的一些查询方法|
|2|ITaskService|任务相关service，包括查询待办、已办，等|

##### <span id="bpm_7_3">3）接口列表</span>

**IInstanceService**

|---|----|----|----|----|
|编号|方法|入参|出参|描述|
|1|startBpm|StartBpmParam|StartBpmResult|启动流程，启动参数中有不同情况的处理|
|2|terminateBpm|userId,instanceId|~|终止流程
|2|terminateBpm|userId,instanceId,comment|~|终止流程
|3|queryTransferDetail|instanceId|`List<ProcessTransferDetail>`|获取流转明细|
|4|getProcessVariables|instanceId|`List<Variable>`|获取流程变量|
|5|queryProcessInstance|processKey|`List<ProcessInstance>`|获取指定流程key对应的运行中的流程实例|

**ITaskService**

注：page对象为分页对象，结果包含分页信息、排序信息及结果content；

|---|----|----|----|----|
|编号|方法|入参|出参|描述|
|1|queryTodo|userId,pageable|`Page<TaskTodo>`|分页查询待办列表|
|1|queryTodo|userId,processDefineKey,pageable|`Page<TaskTodo>`|~|
|1|queryTodo|QueryTaskParam,pageable|`Page<TaskTodo>`|QueryTaskParam对象通过instance静态方法构造，用流式api设置值，如QueryTaskParam param = QueryTaskParam.instance().userId(userId).processDefineKey(processKey);
|2|getTaskTodo|taskId|`TaskTodo`|按照taskId查询待办任务|
|3|queryDone|userId,pageable|`Page<TaskDone>`|分页查询已办列表|
|3|queryDone|userId,processDefineKey,pageable|`Page<TaskDone>`|~|
|3|queryDone|QueryTaskParam,pageable|`Page<TaskDone>`|
|4|getTaskDone|taskId|`TaskDone`|根据taskId查询已办任务|
|5|countTask|userId|`TaskCount`|查询待办任务总数和各个流程定义下的待办总数|
|6|completeTask|taskId,userId|~|同意审批/完成任务|
|6|completeTask|taskId,userId,variables|~|~|
|6|completeTask|taskId,userId,comment|~|~|
|6|completeTask|taskId,userId,comment,variables|~|~|
|7|completeTask|taskId,userId, nextOrgId,comment,variables|~|~|
|7|completeAndAssign|taskId,userId,nextParticipates|~|提交任务并指派下一节点参与人
|7|completeAndAssign|taskId,userId,nextParticipates|~|~
|7|completeAndAssign|taskId,userId,nextParticipates,variables|~|~
|8|addSign|taskId,userIds|~|给当前任务加签|
|8|addSign|taskId,userIds,comment|~|~
|9|reject|taskId,userId|~|驳回到上一节点
|9|reject|taskId,userId,variables|~|
|9|reject|taskId,userId,comment|~|
|9|reject|taskId,userId,comment,variables|~|
|10|rejectToFirst|taskId,userId|~|驳回到制单节点
|10|rejectToFirst|taskId,userId,variables|~|
|10|rejectToFirst|taskId,userId,comment|~|
|10|rejectToFirst|taskId,userId,comment,variables|~|
|11|rejectToSpecified|taskId,userId,activityId|~|驳回到指定节点
|11|rejectToSpecified|taskId,userId,activityId,variables|~|
|11|rejectToSpecified|taskId,userId,activityId,comment|~|
|11|rejectToSpecified|taskId,userId,activityId,comment,variables|~|
|12|delegate|taskId,userId,toUserId|~|转办任务
|12|delegate|taskId,userId,toUserId,comment|~|

##### <span id="bpm_7_4">4) Model说明</span>

- [a、BpmOperateParam](#BpmOperateParam)
- [b、Variable](#Variable)
- [c、AssignInfo](#AssignInfo)
- [d、Activity](#Activity)
- [e、Comment](#Comment)
- [f、UserType（枚举）](#UserType)
- [g、Participant](#Participant)
- [h、StartBpmParam](#StartBpmParam)
- [i、ProcessInstance](#ProcessInstance)
- [j、Operation](#Operation)
- [k、ProcessTransferDetail](#ProcessTransferDetail)
- [l、TaskDone](#TaskDone)
- [m、TaskTodo](#TaskTodo)
- [n、OperationType（枚举）](#OperationType)

**<span id="BpmOperateParam">a、BpmOperateParam</span>**

在添加`BpmOperateListener`时出现，为before/after等方法的入参。

    {
      "comment": "string",  //审批意见
      "definitionId": "string", //流程定义id
      "instanceId": "string",   //流程实例ID
      "operatorId": "string",   //任务操作人Id（必须）
      "taskId": "string",       //任务Id
      "userIds": [              //需要用户信息时添加，比如指派/加签
        "string"
      ],
      "variables": [            //变量
        {
          "name": "string",
          "type": "string",     //变量类型，如string/boolean/integer等
          "value": "string"
        }
      ]
    }

**<span id="Variable">b、Variable</span>**

    {
        "name": "string",
        "type": "string",     //变量类型，如string/boolean/integer等
        "value": "string"
    }

**<span id="AssignInfo">c、AssignInfo</span>**

    {
        "userIds": "String[]", //被指派的用户id数组
    }

**<span id="Activity">d、Activity</span>**

    {
      "id": "string",
      "name": "string",
      "first": "boolean",   //是否流程的第一个activity
      "last": "boolean",   //是否流程的最后一个activity
    }

**<span id="Comment">e、Comment</span>**

    {
      "id": "string",
      "author": "string", //评论人
      "message": "string",
      "createTime": "Date"
    }

**<span id="UserType">f、UserType（枚举）</span>**

    {
        USER,
        DEPT,
        ORG,
        USER_GROUP
    }

**<span id="Participant">g、Participant</span>**

    {
      "id": "string",  //用户id
      "code": "string", //用户表尼玛
      "name": "string",   //用户名称
      "org": "string",   //用户所在机构Id
      "orgName": "string",   //用户所在机构名称
      "type": "UserType" //用户类型
    }

**<span id="StartBpmParam">h、StartBpmParam</span>**

    {
      "userId": "string",
      "processKey": "string", //流程定义key
      "bussinessKey": "string",   //业务单号
      "title": "string",   //流程实例标题
      "orgId": "string",       //制单人机构ID
      "toNext": "boolean",      //是否跳过第一个activity
      "variables": "List<Variable>",  //变量
      "assignInfo": "AssignInfo"        //启动时的指派信息
    }

**<span id="ProcessInstance">i、ProcessInstance</span>**

    {
      "id": "string",  //流程实例id
      "title": "string", //流程实例名称
      "businessKey": "string",
      "definitionId": "string",       //流程定义id，包括版本信息等
      "definitionName": "string",       //流程定义名称
      "currentActivity": "Activity",   //当前所在节点
      "startParticipant": "Participant",      //启动人信息
    }

**<span id="Operation">j、Operation</span>**

    {
        "op", "OperationType",  //操作按钮类型
        "text", "String",       //按钮名称
        "isPlural": "boolean",  //是否需要多个用户id做参数
        "url", "String",        //操作按钮对应的url
        "isNeedUserId": "boolean",  //入参是否需要用户id
        "userRange", "List<String>",    //可选的用户范围
    }

**<span id="ProcessTransferDetail">k、ProcessTransferDetail</span>**

    {
      "id": "string",  //任务id
      "name": "string", //任务节点名称
      "executionId": "string",       //任务执行id
      "createTime": "Date",   //创建时间
      "startTime": "Date",   //创建时间
      "endTime": "Date",
      "durationInMillis": "Long", //持续时间
      "dueDate": "Date",   //持续时间
      "deleteReason": "string",       //待办删除原因，比如处理了，驳回了，终止了，转办了等
      "processInstance": "ProcessInstance",//流程实例
      "participant": "Participant",      //任务参与人信息
      "comment": "Comment"   //评论   
    }

**<span id="TaskDone">l、TaskDone</span>**

    {
      "id": "string",  //任务id
      "name": "string", //任务节点名称
      "executionId": "string",       //任务执行id
      "startTime": "Date",   //创建时间
      "endTime": "Date",
      "durationInMillis": "Long", //持续时间
      "dueDate": "Date",   //持续时间
      "deleteReason": "string",       //待办删除原因，比如处理了，驳回了，终止了，转办了等
      "processInstance": "ProcessInstance",//流程实例
      "assigneeParticipant": "Participant",      //任务参与人信息
      "comment": "Comment",   //评论
      "pcUrl": "string",
      "mobileUrl": "string"       //移动审批Url    
    }

**<span id="TaskTodo">m、TaskTodo</span>**

    {
      "id": "string",  //任务id
      "name": "string", //任务节点名称
      "createTime": "Date",   //创建时间
      "dueDate": "Date",   //持续时间
      "executionId": "string",       //任务执行id
      "processInstance": "ProcessInstance",//流程实例
      "operations": "List<Operation>",      //任务可执行的操作
      "activity": "Activity",   //任务对应节点的描述对象
      "pcUrl": "string",
      "mobileUrl": "string"       //移动审批Url    
    }

**<span id="OperationType">n、OperationType（枚举）</span>**

|----|----|----|----|
|按钮名称(默认名称)|code|流程设计图对应任务节点功能设置|描述|
|同意|agree|下一步|同意审批|
|拒绝|refuse||
|驳回|reject|退回上一步|
|驳回到第一步|rejectToFirst|退回第一步|
|驳回到指定环节|rejectToSpecified|退回指定节点|
|改派|delegate|转发|
|加签|addSign|加签|
|指派|assign||
|终止|terminate|终止流程|
|挂起|suspend|挂起流程|
|激活|activate|激活流程|
|减签|delSign|减签|

#### <span id="bpm_8">3.3.8 异常说明</span>

|----|----|----|
|错误码|错误提示|描述|
|**500**|查询待办发生异常|流程引擎内部错误，可能原因为：1、参数错误，2、流程引擎bug|

#### <span id="bpm_9">3.3.9 同步模块说明</span>

（待补充）

#### <span id="bpm_10">3.3.10 接口使用统计</span>

说明：竖列标识系统列表，横列标识对外接口，中间内容区域，`~`标识该系统未用对应接口

|---|----|----|----|----|----|----|----|----|----|
|系统|startBpm|queryTransferDetail|getProcessVariables|queryProcessInstance|queryTaskTodo|getTaskTodo|queryDone|getTaskDone|
|档案系统|
#### <span id="bpm_11">3.3.11 版本变更历史</span>

|----|----|----|
|SDK版本|发布时间|更新内容描述|
|0.0.1SNAPSHOT||新建项目|

#### <span id="bpm_12">3.3.12 工作流平台架构图</span>

![流程引擎架构图](https://i.imgur.com/ImuJSR1.jpg)

流程引擎采用中心部署的模式，作为开发平台的子模块提供服务。业务系统可以:
    
   - 1、通过统一管理台设计发布流程，跟踪流程（实例），并维护人员、机构和角色等数据；
   - 2、通过开发平台流程引擎模块（chamc--boot-starter-bpm，简称BPM模块）SDK的方式引入项目并控制流程及任务，如发起流程、同意审批、加签等。

BPM模块封装了统一的流程服务，能够提供流程执行与控制、任务执行与管理、流程定义、流程历史控制及自由流等服务。业务系统可调用开发平台SDK服务与统一流程服务层交互。  
流程服务通过流程引擎的驱动与数据进行交互。在数据管理方面，流程引擎进行统一的数据管理控制，如统一的人员基本信息管理、组织机构基本信息管理以及人员-机构信息管理等，并通过异步的数据同步服务维护基本数据。  
流程数据和机构数据分租户隔离，对于机构数据来说，不同租户间的数据隔离通过关联关系确定，因此机构的基本数据是统一一致的，流程定义、实例信息等数据分离，避免各个系统间的流程混乱。  
统一管理平台提供了流程定义管理、流程实例管理、用户机构和角色等的管理，使用时在改平台下完成流程的定义和部署，并能够进行流程的追踪。管理平台可进行简单的人员、机构和角色等的维护，批量的数据则通过数据同步服务进行处理。  
各个组件之间的有机协作，为业务系统提供提供使用简单、管理方便的流程引擎服务。


><span id="fz">**附注：**</span>

>技术支持，座机：010-57326502，邮件：[miaoshipeng@chamc.com.cn](mailto:miaoshipeng@chamc.com.cn)

>流程设计器试用地址：[http://10.1.8.117:8080/workflow-console](http://10.1.8.117:8080/workflow-console)，账号：请与我们联系申请！
