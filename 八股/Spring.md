### Controller和RestController的区别

共同点：都是用来表示Spring某个类的是否可以接收HTTP请求

不同点：
- @Controller：标识一个Spring类是Spring MVC controller处理器
- @RestController是@Controller和@ResponseBody的结合体，两个标注合并起来的作用。

@Controller类中的方法可以直接通过返回String跳转到jsp、ftl、html等模版页面。在方法上加@ResponseBody注解，也可以返回实体对象。

@RestController类中的所有方法只能返回String、Object、Json等实体对象，不能跳转到模版页面。
@RestController中的方法如果想跳转页面，则用`ModelAndView`进行封装