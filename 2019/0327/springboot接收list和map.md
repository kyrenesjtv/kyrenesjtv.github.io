### springboot controller 接收单个List

#### controller 接收 List POST

```
    /**
     * 根据部门ID删除部门信息
     * @return
     */
    @RequestMapping( value = "/deleteDepartmentByIds" , method = RequestMethod.POST)
    public Integer deleteDepartmentByIds(@RequestBody List<String> departIds){
        Integer deleteResult = departmentService.deleteDepartmentByIds(departIds);
        return deleteResult;
    }
```

#### ajax

```
    //$.toJSON需要引入jquery.json.min.js 并放在jquery下面
    $.ajax({
        url:"../department/deleteDepartmentByIds",
        type:"post",
        contentType : 'application/json;charset=utf-8',
        dataType: 'json',
        data:$.toJSON(departIds),
        success:function(data){

        }
        })
```

#### controller 接收map  GET

```
    /**
     * 根据部门ID删除部门信息
     * @return
     */
    @RequestMapping( value = "/deleteDepartmentByIds" , method = RequestMethod.GET)
    public Integer deleteDepartmentByIds(@RequertParam Map<String,String> map){
        Integer deleteResult = departmentService.deleteDepartmentByIds(map);
        return deleteResult;
    }
```

#### ajax

```
    var map = {};
    map["deptIds"] = deptIds;
    $.ajax({
        url:"../department/deleteDepartmentByIds",
        type:"get",
        contentType : 'application/json;charset=utf-8',
        dataType: 'json',
        data:{map:map},
        success:function(data){

        }
        })
```
####  写在最后, controller 这样子接收Map 之后， 需要map.get("map[deptIds]") 这样子才能取到值， 而不能 map.get("deptIds") 。 甚是费解