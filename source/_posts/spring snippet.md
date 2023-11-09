---
title: spring snippet
date: 2016-10-16 16:01:30
tags: snippet
categories: spring
---

### 知识点

#### 传JSON字符串+@RequestBody接收

- 前端
  
  ```java
  function icheckDelete(url){
    var parms = {
        list : array //这是个数组
    };
  
    $.ajax({
        dataType: "JSON",
        contentType:'application/json;charset=UTF-8',//关键是要加上这行
        traditional:true,//这使json格式的字符不会被转码
        data: JSON.stringify(parms),
        type: "DELETE", 
        timeout: 20000,
        url: url,
        success : function () {
            alert("删除成功！");
        },
        error : function (data){
            alert(data.responseText);
        }
    });
  ```

}

```java
- 后端
Controller:

@RequestMapping(value = "deleteList", method = RequestMethod.DELETE)
@ResponseBody
public String delete(@RequestBody DeleteListRequest request) throws WebTransException{

    /* 逻辑代码 */

    return "success";
}
```

## RESTFUL

-  `@RestController` class level annotation and then add the following `@RequestMapping` to the class