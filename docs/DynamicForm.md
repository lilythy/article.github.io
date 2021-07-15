# 动态表单
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
1. 获取renderContent 和 fieldData；
2. form外层先写好，并传入field={this.field}，如下：
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
3. 接下来就是写form表单里的每项item了，拿到renderContent后遍历，根据component转换成对应form表单组件，name, title, required, disabled，rules这些属性可以直接加上，再加上一些固定的属性如autoWidth, hasClear
   
然后defaultValue取fieldData[relevanceCode][value]，

有childName时，onChange时要将childName那个组件值设为undefined，

如果是component是“select”，“treeSelect”组件则用basicSelect组件，有url时设置url，有parent的话设置params={{[parent]: this.field.getValue(parent)}}, 如果没有url，取dataSource

4. 除后端返回的数据之外，如果要外部联动数据，则可以通过this.field.setValue(s)来设置对应的值，并修改fieldData里对应的值
5. 如果要加额外的校验，也可以获取renderContent之后，再修改里面的数据，添加上需要的rules规则
6. 如果要添加组件或删除组件，也可以修改renderContent来达到目的
7. 如果要改布局，可以通过复写css
