# 盒马动态表单

renderContent:

```javascript
{
  "code": "preview", // 模块key
  "isParse": "N",
  "isVisible": "Y",  // 模块是否可见
  "desc": "\u5de5\u4f5c\u4fe1\u606f", // 模块名称
  "status": "Y", // 该模块是否显示
   "fields": [
     {
       "required": true, // 是否必填
       "disabled": false,  // 是否可编辑
       "childName": "empSubType", // 依赖子组件的key,用于修改或清空时，同时清空子组件的值
       "name": "empType", // key
       "relevanceCode": "empEmployee",  // 关联value数据object的key
       "title": "\u7528\u5de5\u7c7b\u578b",  //表单label名称
       "component": "input",  // 前端用什么组件，根据不同的类型渲染不同的组件
       "value": "empTypeValue", // 取关联value数据object里relevanceCode的对象里该key的值
       "feIndex": 2,
       "url": "//hrworkbench.hemaos.com/emp/promoter/combox/emptype", // 如果是下拉框的话,有url则用该url请求数据，否则用dataSource数据
       "dataSource": [{label: 'test', value: 1}],
       "status": "Y",  // 模块是否显示
       "parent": "refProjectId",  // url请求传参是否依赖parent的值，有的话params里设置
       "rules": [{message: "请输入零售商商品编码", required: true}] // 校验
     },
   ]
}
```

fieldData:

```javascript
"data": {
  "empEmployee": {   // 这个key为renderContent里的relevanceCode
    "id": 186219,  // 这里面的key为renderContent里value
    "empId": 186219,
    "personId": 1680,
    "workNo": "CSC00001006",
    "extWorkNo": null,
    "name": "\u987e\u6210\u6e90",
    "nickName": null,
    "empType": "WB",
    "empTypeValue": "\u5916\u5305",
    "empSubType": "WB01",
    "empSubTypeValue": "\u4fc3\u9500\u5458",
    "gmtEntry": "2020-01-07",
    "gmtActive": null,
    "gmtOrigRegular": null,
    "gmtRegular": null,
    "workStatus": "INACTIVE",
    "workStatusValue": "\u5df2\u79bb\u804c",
    "workPhoto": null,
    "personalPhoto": null,
    "isOpenAliEmail": null,
    "isOpenAliEmailValue": "",
    "isOpenHomsAccount": null,
    "isOpenHomsAccountValue": "",
    "merchantCode": "CSSJ",
    "merchantCodeValue": null,
    "bucLocationNo": null,
    "bucLocationNoValue": "",
    "source": null,
    "sourceValue": "",
    "gmtDimissionActive": "2020-01-13",
    "electronicArchives": {
      "PERSONAL_PHOTO": "https://static.dingtalk.com/media/lADPDgQ9rby8jjrNAyDNAoE_641_800.jpg"
    },
    "empExtType": "CC",
    "empExtTypeValue": "\u957f\u4fc3"
  },
}
```

**具体实现：**

1. 获取 renderContent 和 fieldData；
2. form 外层先写好，并传入 field={this.field}，如下：

```html
<Form
  isPreview={linkType === 'view'}
  labelCol={labelCol}
  wrapperCol={16}
  field={this.field}
  // needElevator
  >
  {this.renderFieldSet()}
</Form>
```

3. 接下来就是写 form 表单里的每项 item 了，拿到 renderContent 后遍历，根据 component 转换成对应 form 表单组件，name, title, required, disabled，rules 这些属性可以直接加上，再加上一些固定的属性如 autoWidth, hasClear

然后 defaultValue 取 fieldData[relevanceCode][value]，

有 childName 时，onChange 时要将 childName 那个组件值设为 undefined，

如果是 component 是“select”，“treeSelect”组件则用 basicSelect 组件，有 url 时设置 url，有 parent 的话设置 params={{[parent]: this.field.getValue(parent)}}, 如果没有 url，取 dataSource

4. 除后端返回的数据之外，如果要外部联动数据，则可以通过 this.field.setValue(s)来设置对应的值，并修改 fieldData 里对应的值
5. 如果要加额外的校验，也可以获取 renderContent 之后，再修改里面的数据，添加上需要的 rules 规则
6. 如果要添加组件或删除组件，也可以修改 renderContent 来达到目的
7. 如果要改布局，可以通过复写 css

# 笨马动态表单

## 一、字段类型

字段类型 类型说明

text 短文本类型，前端表单类型 `<input type="text"/>`

longtext 长文本类型，前端表单类型 `<textarea>`

number 数值类型，前端表单类型 `<input type="number"/>`

bool bool 类型，前端表单类型 `<input type="checkbox"/>`

hostname 主机类型，前端表单类型 `<select>`，数据来源从后端 API 查询

password 密码类型，前端表单类型 `<input type="paasword"/>`

option 单选类型 ，前端表单类型 `<select>`
complex 复合类型

## 二、公共属性

名称 类型 空字段 说明

name String 否 字段名称<br>
type String 否 字段类型，参考【字段类型】定义<br>
label String 否 显示标签<br>
required Boolean 否 是否必须<br>
defaultValue String 是 默认值<br>
description String 是 字段描述<br>
group String 是 字段分组<br>
subPropertys List 是 子字段集合<br>
showSubPropertyIf String 是 子字段显示条件，示例：showSubPropertyIf: "true"
showSubPropertyIf: "NodePort"

showIf String 是 当前字段显示条件：示例：showIf: "kafka-topics-ui.enabled=true&&global.enterprise=false"

**_【text】类型属性 _**
<br>
名称 类型 空字段 说明<br>
min Integer 是 输入最小长度<br>
max Integer 是 输入最大长度<br>

**_【longtext】类型属性 _**
<br>
名称 类型 空字段 说明<br>
min Integer 是 输入最小长度<br>
max Integer 是 输入最大长度<br>
codeblock String 否 内容格式，可用于高亮显示，取值：text、shell、sql、yaml、json、properties、xml

**_【number】类型属性 _**
<br>
名称 类型 空字段 说明<br>
min Integer 是 输入最小值<br>
max Integer 是 输入最大值<br>

**_【hostname】类型属性 _**
<br>
名称 类型 空字段 说明<br>
arrayValue Boolean 否 是否是数组格式

**_【password】类型属性 _**
<br>
名称 类型 空字段 说明<br>
min Integer 是 输入最小长度<br>
max Integer 是 输入最大长度<br>

**_【option】类型属性 _**
<br>
名称 类型 空字段 说明<br>
options String[] 否 单选项值

**_【complex】类型属性 _**
<br>
名称 类型 空字段 说明<br>
arrayValue Boolean 否 是否是数组格式

**_ 配置定义参考 _**

```javascript
{
  "standalone": [
    {
      "name": "host",
      "type": "hostname",
      "arrayValue": false,
      "label": "部署服务器",
      "required": true
    },
    {
      "name": "password",
      "type": "password",
      "max": 100,
      "label": "连接密码",
      "required": false,
      "defaultValue": "Perfma888."
    },
    {
      "name": "flink-conf.yaml",
      "type": "longtext",
      "min": 0,
      "max": 50000,
      "codeblock": "yaml",
      "label": "Flink配置",
      "required": false,
      "defaultValue": "akka.ask.timeout:30s\nakka.lookup.timeout:30s\nakka.watch.heartbeat.interval:20s\ntaskmanager.memory.jvm-metaspace.size: 512m",
      "description": "Flink配置格式yaml, 谨慎修改)"
    },
    {
      "name": "zookeeper-conf.conf",
      "type": "longtext",
      "min": 0,
      "max": 50000,
      "codeblock": "properties",
      "label": "Zookeeper配置",
      "required": false,
      "defaultValue": "tickTime=2000\ninitLimit=5\nsyncLimit=2\nautopurge.snapRetainCount=3\nautopurge.purgeInterval=1\nmaxClientCnxns=300",
      "description": "Zookeeper配置格式properties, 谨慎修改"
    },
    {
      "name": "jobmanager",
      "type": "complex",
      "arrayValue": false,
      "required": true,
      "group": "JobManager",
      "subPropertys": [
        {
          "name": "jobmanager.host",
          "type": "hostname",
          "label": "部署服务器",
          "required": true,
          "group": "JobManager"
        },
        {
          "name": "jobmanager.memorySize",
          "type": "text",
          "label": "总内存大小",
          "required": false,
          "defaultValue": "1600m",
          "group": "JobManager"
        }
      ]
    },
    {
      "name": "taskmanagers",
      "type": "complex",
      "arrayValue": true,
      "required": true,
      "group": "TaskManagers",
      "subPropertys": [
        {
          "name": "taskmanagers.host",
          "type": "hostname",
          "label": "部署服务器",
          "required": true,
          "group": "TaskManagers"
        },
        {
          "name": "taskmanagers.memorySize",
          "type": "text",
          "label": "总内存大小",
          "required": false,
          "defaultValue": "6g",
          "group": "TaskManagers"
        },
        {
          "name": "taskmanagers.numberOfTaskSlots",
          "type": "number",
          "label": "任务计算槽位",
          "required": false,
          "defaultValue": "20",
          "group": "TaskManagers"
        }
      ]
    }
  ],
  "external": [
    {
      "name": "mode",
      "type": "option",
      "label": "连接模式",
      "required": true,
      "options": [
        "single",
        "sentinel",
        "cluster"
      ],
      "description": "Redis连接模式: 单机(single) | 哨兵(sentinel) | 集群(cluster)"
    },
    {
      "name": "password",
      "type": "text",
      "label": "连接密码",
      "required": true
    },
    {
      "name": "address",
      "type": "text",
      "label": "连接地址",
      "required": true,
      "showIf": "mode=single"
    },
    {
      "name": "sentinelMaster",
      "type": "text",
      "label": "哨兵Master",
      "required": true,
      "showIf": "mode=sentinel"
    },
    {
      "name": "sentinelNodes",
      "type": "text",
      "label": "哨兵节点地址",
      "required": true,
      "description": "哨兵节点地址(示例: 192.168.x.10:26379,192.168.x.11:26379,192.168.x.12:26379)",
      "showIf": "mode=sentinel"
    },
    {
      "name": "clusterNodes",
      "type": "text",
      "label": "集群节点地址",
      "required": true,
      "description": "集群节点地址(示例: 192.168.x.10:9092,192.168.x.11:9092,192.168.x.12:9092)",
      "showIf": "mode=cluster"
    },
    {
      "name": "metrics.enabled",
      "type": "bool",
      "label": "启用监控采集",
      "required": false,
      "defaultValue": "false",
      "showSubPropertyIf": "true",
      "subPropertys": [
        {
          "name": "metrics.module",
          "type": "option",
          "label": "Blackbox探测模块",
          "options": [
            "none",
            "http_2xx",
            "tcp_connect"
          ],
          "defaultValue": "none",
          "required": true,
          "group": "监控采集配置"
        },
        {
          "name": "metrics.host",
          "type": "text",
          "max": 50,
          "label": "数据采集HOST",
          "required": true,
          "group": "监控采集配置"
        },
        {
          "name": "metrics.port",
          "type": "number",
          "label": "数据采集端口",
          "required": true,
          "group": "监控采集配置"
        },
        {
          "name": "metrics.path",
          "type": "text",
          "max": 100,
          "label": "数据采集路径",
          "required": true,
          "group": "监控采集配置",
          "showIf": "metrics.module!=tcp_connect"
        },
        {
          "name": "metrics.interval",
          "type": "number",
          "label": "数据采集间隔时间",
          "required": false,
          "group": "监控采集配置"
        },
        {
          "name": "metrics.timeout",
          "type": "number",
          "label": "数据采集超时时间",
          "required": false,
          "group": "监控采集配置"
        },
        {
          "name": "metrics.upMetric",
          "type": "text",
          "max": 30,
          "label": "自定义健康状态指标",
          "required": false,
          "group": "监控采集配置"
        }
      ]
    }
  ]
}
```

**_ Rancher 应用部署配置定义参考 _**
<br>
https://10.10.227.26:8443/g/apps/catalog/cattle-global-data:library-kafka<br>
https://10.10.227.26:8443/g/apps/catalog/cattle-global-data:library-redis
