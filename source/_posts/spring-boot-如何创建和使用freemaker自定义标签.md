---
title: spring boot 如何创建和使用freemaker自定义标签
date: 2019-02-19 18:06:11
tags:
---

由于spring boot 简化了配置文件的，所以之前在配置文件中实现自定义freemaker标签，统一通过Java类实现。

之前spring MVC 的实现步骤：

    <!-- =========================freemarker VIEW定义 ========================= -->
    <bean id="freeMarkerConfig" class="org.springframework.web.servlet.view.freemarker.FreeMarkerConfigurer">
       <property name="templateLoaderPaths" value="/WEB-INF/view2.0/"/>
       <property name="freemarkerVariables">
          <map>
             <!-- shiro freemarker标签  使用自定义标签-->
             <entry key="shiro">
                <bean class="com.yuelinghui.shiro.freemarker.ShiroTags"/>
             </entry>
          </map>
       </property>
    </bean>
    那么spring boot 中如何实现，具体实现如下：
    
    创建 FreemarkerConfig ，
    @Component
    public class FreemarkerConfig {
    
      @Autowired
      private Configuration configuration;
    
      @PostConstruct
      public void setSharedVariable() {
    // 创建自定义标签对象
        configuration.setSharedVariable("shiro", new ShiroTags());
      }
    
    }
    通过上述方法可以实现引入freemaker 自定义标签
