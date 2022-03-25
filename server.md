## rasa nlu server
```
rasa nlu server

# 本地模型路由 - 分项目
DataRouter()
	# 属性
	self.project_store
		{
			"default": Project(default_model_dir)
		}

	# 方法 - 预加载模型
	self._pre_load(project_name):
		Project().load_model()

	# 方法 - 分析方法
	self.parse(data):
		Project().parse()


# 项目模型 - 抽象
Project()
	# 属性
	self._models
		{
			"model_20220324-125559": None,
			"model_20220325-125559": None
		}

	# 方法 - 预加载模型核心逻辑
	self.load_model():
		 self._models
		 	{
				"model_20220324-125559": None,
				"model_20220325-125559": Interpreter.create(model_metadata, self._component_builder)
			}

	# 方法 - 模型分析
	self.parse(text, model_name):
		Interpreter.create(model_metadata, self._component_builder).parse(text)



# 模型元数据类 - 抽象
Metadata()
	# 属性
	self.metadata - metadata.json data
	self.model_dir

	# 方法 - 获取模型components class
	self.component_classes():
	    if self.get('pipeline'):
            return [c.get("class") for c in self.get('pipeline', [])]
        else:
            return []

    # 方法 - 加载metadata.json
    def load(model_dir):
        return Metadata(data, model_dir)

# 组件构建类
ComponentBuilder()
	# 方法
	self.load_component(component_name):
		return component_name()


# 模型解析类 - 抽象(model + pipeline)
Interpreter()
	# 属性
	self.pipeline - 存放构建好的pipeline
	self.context - 组件需要的额外参数
	self.model_metadata - 存放着用户自定义的组件集的导入地址和依赖文件

	# 方法
	self.create(model_metadata, component_builder)
		//参数 model_metadata - 存放着用户自定义的组件集的导入地址和依赖文件
		//参数 component_builder - 构建pipeline依赖
		for component in model_metadata.component_classes():
			pipeline,append(ComponentBuilder().load_component(component))
		retrue Interpreter(pipeline, context, model_metadata)

	# 方法 - 模型分析
	self.parse(text):
		# 封装成Message对象 - 在component间传输 
		message = Message(text)
		for component in self.pipeline:
            component.process(message, **self.context)
        retrun message

```







