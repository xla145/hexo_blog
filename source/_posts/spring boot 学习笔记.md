---
title: spring boot 学习笔记
date: 2019-02-18 17:06:43
---
1：Spring Boot 定制URL匹配规则的方法

昨天使用spring boot 做了一个后台管理系统，再方法请求时由于带了后缀导致不能找到对应的controller。原因是spring boot 路由规则设置了真匹配，比如 请求你发起请求 /index ，它只能匹配 /home 路径的 带后缀的一律不匹配。那么如果想达到匹配需求以下步骤：

1：启动类 extends WebMvcConfigurationSupport

2：重写configurePathMatch方法

    @SpringBootApplication
    public class DreamAdminApplication extends WebMvcConfigurationSupport {
    
    /**
     * 1、 extends WebMvcConfigurationSupport
     * 2、重写下面方法;
     * setUseSuffixPatternMatch : 设置是否是后缀模式匹配，如“/index ”是否匹配/index.*，默认真即匹配；
     * setUseTrailingSlashMatch : 设置是否自动后缀路径模式匹配，如“/index ”是否匹配“/index /”，默认真即匹配；
     */
    @Override
    public void configurePathMatch(PathMatchConfigurer configurer) {
        configurer.setUseSuffixPatternMatch(true);
    }
    
    
    @Override
    public void addResourceHandlers(ResourceHandlerRegistry registry) {
        registry.addResourceHandler("/static/**").addResourceLocations("classpath:/static/");
        super.addResourceHandlers(registry);
    }
    
    public static void main(String[] args) {
        SpringApplication.run(DreamAdminApplication.class, args);
    }
    }
2：Spring Boot 自定义静态资源映射目录

由于继承了WebMvcConfigurationSupport这个类，导致静态文件出现404的问题，所以需要我们自定义静态资源映射目录的，具体实现只需要重写（WebMvcConfigurationSupport ）的addResourceHandlers方法即可。

registry.addResourceHandler(“/static/**”).addResourceLocations(“classpath:/static/“);

关于 WebMvcConfigurationSupport 提供的方法，后续再一一讲解。