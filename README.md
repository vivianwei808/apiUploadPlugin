# apiUploadPlugin
## 目的
减少pmo-api 录入时间，通过工具反向规范代码注释，和代码整洁

## 功能
单个&批量
单个上传选中方法名称
批量上传选中类名

## 使用方式

### 1: 下载jar包 
### 2: 需要 打开idea preferneces->plugins-> install plugin from disk,导入jar 包后(install)，重启
### 3: 配置信息
 ####   3.1: 配置信息位置, 在项目目录下，.idea 文件夹下，找到misc.xml (如果找不到.idea 请查看是否被折叠或被隐藏) 

```
<component name="gdcz-dataengine-app">
  <option name="projectToken">pmo-api 中项目token</option>
  <option name="projectId">pmo-api 中项目id</option>
  <option name="pmoApiUrl">http://127.0.0.1:3000</option>
  <option name="returnClass">com.project.Response</option>
</component>
```

#### 3.4: 各个参数获得

    token 获取方式： 打开pmo-api ->具体项目->设置->token 配置
    项目id 获取方式：点击项目，查看url 中project 后面的数字为项目id http://127.0.0.1:3000/project/72/interface/api
    pmoApiUrl 获取方式：部署的pmo-api 地址
    returnClass 填写方式:统一返回对象的全路径

#### 3.5: 上传

    如果是api 项目，选中controller 类中的方法名称或类名（要选中方法名称，或类名，选中类名为当前类所有接口都上传），右击YapiUpload(alt+u快捷键)
    如果未选择任何地方 上传 默认按类上传
   
### 规则
    注意点: 良好的java doc 注释能生成更好的文档

#### 1:插件如何生成属性备注 ,通过获得字段备注中的注释
```
/** 
* 年龄
*/
private Integer age;
```

#### 2:插件如何生成接口名称，通过接口上的注释，或者引用上的 注释

```
/**
     * 添加或更新课程数据
     *
     * @param courseOpt
     * @return {@link CommonRes}
     */
    @RequestMapping(value = "/test", method = RequestMethod.POST)
    public Course addOrUpdateCourse(@RequestBody CourseParam courseParam){
    
    }
    
    
    /**
     * @description: 添加或更新课程数据  (@Description 也可）
     * @param: [CourseParam]
     * @return: Course
     * @date: 2018/3/15
     */
     @RequestMapping(value = "/test", method = RequestMethod.POST)
     public Course addOrUpdateCourse(@RequestBody CourseParam courseParam){
    
    }

    
     
     @RequestMapping(value = ICourse.ADD_OR_UPDATA_COURSE, method = RequestMethod.POST)
     public Course addOrUpdateCourse(@RequestBody CourseParam courseParam){
    
     }

     public interface  ICourse{
       /**
        * @description: 添加或更新课程数据  (@Description 也可）
        * @param: [CourseParam]
       */
       ADD_OR_UPDATA_COURSE="test";
     }
```
#### 3:@link 参数定义展示在字段备注中
```
第一种@link 方式

/** 
* 状态 {@link com.xxx.constant.StatusConstant}
*/
private Integer status;


第二种@link 方式

import com.xxx.constant.StatusConstant;

/** 
* 状态 {@link StatusConstant}
*/
private Integer status;


不支持方式
import com.xxx.constant.*;

/** 
* 状态 {@link StatusConstant}
*/
private Integer status;
```

#### 4:实现自定义分类
通过在方法或类注释中加 @menu 注释实现，优先级 方法>类>package 下面或者上面的@menu,如果没有自定义 默认为tool-tmp,支持多级分类（多级分类只能是crazy 分支的yapi)

```
package com.project.demo;
/** 
 * @menu 这里填写类分类名称
 */
import com......
/** 
 *@description: 用户控制器
 *@menu 这里填写类分类名称
 */   
@RestController
public class UserController {

    /**
     * @description: 新增用户
     * @param: [User]
     * @menu: 这里填写方法级别分类名称（比如 多级目录/menu/menu1/menu2)
     * @return: Response<UserDTO>
     * @date: 2018/3/15
     */
     @RequestMapping(value = "/addUser", method = RequestMethod.POST)
     public Response<UserDTO> addUser(@RequestBody User user){
    
    }
}
```

#### 5:支持注解

```
String RequestMapping="org.springframework.web.bind.annotation.RequestMapping";

    String GetMapping="org.springframework.web.bind.annotation.GetMapping";

    String PostMapping="org.springframework.web.bind.annotation.PostMapping";

    String PutMapping="org.springframework.web.bind.annotation.PutMapping";

    String DeleteMapping="org.springframework.web.bind.annotation.PutMapping.DeleteMapping";

    String PatchMapping="org.springframework.web.bind.annotation.PutMapping.PatchMapping";

    String RequestBody="org.springframework.web.bind.annotation.RequestBody";

    String RequestParam="org.springframework.web.bind.annotation.RequestParam";

    String RequestHeader="org.springframework.web.bind.annotation.RequestHeader";

    String RequestAttribute="org.springframework.web.bind.annotation.RequestAttribute";
   
    String PathVariable="org.springframework.web.bind.annotation.PathVariable";

    String NotNull="javax.validation.constraints.NotNull";
   
    String NotEmpty="javax.validation.constraints.NotEmpty";

    String NotBlank="javax.validation.constraints.NotBlank";
```

#### 支持Request Param 注解
生成的 uid 和bid 备注为 用户id(Integer),baby id(Integer) 如果没有备注，则只有(Integer)
```
/**
     * 测试RequestHeader desc 规范的多个
     * @param uid 用户id
     * @param bid baby id
     * @param request
     * @param response
     */
    @RequestMapping(value = "/testRequestDesc4")
    public void testRequestDesc4(@RequestHeader(name = "uid")Integer uid, @RequestParam(name = "bid")Integer bid, HttpServletRequest request, HttpServletResponse response){

    }
```

#### 支持@status注解
支持已发布(done),设计中(design),开发中(undone),已提测(testing),已过时(deprecated),暂停开发(stoping),支持中英文 （crazy 分支)，新增接口默认 开发中,更新时如果没有写status情况下默认使用当前状态
```
   /**
     * 测试RequestHeader desc 规范的多个
     * @param uid 用户id
     * @status 开发中 (或者 undone)
     * @param request
     * @param response
     */
    @RequestMapping(value = "/testRequestDesc4")
    public void testRequestDesc4(@RequestHeader(name = "uid")Integer uid, @RequestParam(name = "bid")Integer bid, HttpServletRequest request, HttpServletResponse response){

    }
```
#### 6:项目中通过fitler或拦截器或注解等 实现全局统一响应对象 返回如何处理
在misc.xml 中配置 returnClass ，值为全局统一响应对象全路径。比如下面代码如果配置了returnClass 为GlobalResponse ,那么他生成的yapi 响应对象为，GlobalResponse

```
  /**
     * 测试RequestHeader desc 规范的多个
     * @param uid 用户id
     * @status 开发中 (或者 undone)
     * @param request
     * @param response
     */
    @RequestMapping(value = "/testRequestDesc4")
    public Integer testRequestDesc4(@RequestHeader(name = "uid")Integer uid, @RequestParam(name = "bid")Integer bid, HttpServletRequest request, HttpServletResponse response){

    }


public class GlobalResponse<T> {

    private String status;

    private String code;

    private T data;

  ......
}
```
#### 7:swagger 支持
```

  String API_OPERATION = "io.swagger.annotations.ApiOperation";

  String API_MODEL_PROPERTY = "io.swagger.annotations.ApiModelProperty";

  String API_PARAM = "io.swagger.annotations.ApiParam";
```
#### 8:支持@path
通过@path 注释,如下代码，其生成的路径为/path/test/testRequestDesc4

```
  /**
     * 测试RequestHeader desc 规范的多个
     * @param uid 用户id
     * @path /path/test/testRequestDesc4
     * @param request
     * @param response
     */
    @RequestMapping(value = "/testRequestDesc4")
    public Integer testRequestDesc4(@RequestHeader(name = "uid")Integer uid, @RequestParam(name = "bid")Integer bid, HttpServletRequest request, HttpServletResponse response){

    }
```

