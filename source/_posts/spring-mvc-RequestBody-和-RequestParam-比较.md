---
title: spring mvc @RequestBody 和 @RequestParam 比较
date: 2019-02-19 18:03:23
tags:
---
1： @RequestBody

    @RequestBody 注解 通过 HttpMessageReader 使请求的body反序列化成一个对象。
    
    GET、POST方式提交时， 根据 request header Content-Type 的值来判断:
    
    application/x-www-form-urlencoded， 可选（即非必须，因为这种情况的数据@RequestParam, @ModelAttribute也可以处理，当然@RequestBody也能处理）；
    multipart/form-data, 不能处理（即使用@RequestBody不能处理这种格式的数据）；
    其他格式， 必须（其他格式包括application/json, application/xml等。这些格式的数据，使用@RequestBody来处理）；
    $.ajax({
        type: "POST",
        url: "/coupon/del",
        dataType : 'json',
        contentType : 'application/json;charset=UTF-8', // 设置请求的文本类型
        data: JSON.stringify(ids),
        success: function (result) {
            layer.msg(result.msg)
        }
    });
    控制层接收
    
    public JSONObject del(@RequestBody String[] cids){
           RecordBean<String> result = couponService.delBatch(cids);
       if (result.isSuccessCode()) {
          return JsonBean.success(result.getMsg());
       }
       return JsonBean.error(result.getMsg());
2：@RequestParam

    @RequestParam 
    
    A） 常用来处理简单类型的绑定，通过Request.getParameter() 获取的String可直接转换为简单类型的情况（ String--> 简单类型的转换操作由ConversionService配置的转换器来完成）；因为使用request.getParameter()方式获取参数，所以可以处理get 方式中queryString的值，也可以处理post方式中 body data的值；
    
    B）用来处理Content-Type: 为 application/x-www-form-urlencoded编码的内容，提交方式GET、POST；
    
    C) 该注解有两个属性： name、required； 
    
    name：用来指定要传入值的key名称
    
    required：用来指示参数是否必须绑定；当@RequestParam 被声明是Map<String, String> or MultiValueMap<String, String> required 为false
    
    相比 Request.getParameter() 获取 优点：
    
    通过注解的方式可以减少代码量，代码更整洁
    
    示例代码：
    
    $.ajax({
        type: "POST",
        url: "/coupon/del",
        data: {ids:123},
        success: function (result) {
            layer.msg(result.msg)
        }
    });
    控制层
    
    
     
     * 删除
        */
       @ResponseBody
       @RequestMapping("/dels")
       public JSONObject dels(@RequestParam(name = "ids") Integer cids){
          System.out.println(cids);
          return JsonBean.success("ok",cids);
       }
3:@ModelAttribute 注解获取参数

    /**
        * 删除
        */
       @RequestMapping("/coupon")
       @ResponseBody
    // @RequiresPermissions("coupon:del")
       public JSONObject coupon(@ModelAttribute Coupon coupon){
      return JsonBean.success("",coupon);

