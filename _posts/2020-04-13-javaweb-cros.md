---
layout: post
title: javaweb跨域
categories: [java, javaweb, cros]
description: javaweb设置跨域
keywords: java, javaweb
---

## 设置filter
1. ### 新建网关模块处理跨域
- import org.springframework.web.cors.CorsConfiguration;
- import org.springframework.web.cors.UrlBasedCorsConfigurationSource;
- import import org.springframework.web.filter.CorsFilter;
以上三个包处理跨域

```java
package com.store.gateway.config;

import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.web.cors.CorsConfiguration;
import org.springframework.web.cors.UrlBasedCorsConfigurationSource;
import org.springframework.web.filter.CorsFilter;

/*
 * 设置跨域访问
 * */
@Configuration
public class StoreCorsFilterConfiguration {

    @Bean
    public CorsFilter corsFilter() {
        CorsConfiguration corsConfiguration = new CorsConfiguration(); //初始化配置对象
        corsConfiguration.addAllowedOrigin("http://manage.store.com"); //设置允许跨域域名，设置*时表示所有域名不允许cookie使用
        corsConfiguration.addAllowedOrigin("http://api.store.com"); //设置允许跨域域名，设置*时表示所有域名不允许cookie使用
        corsConfiguration.addAllowedOrigin("http://image.store.com"); //设置允许跨域域名，设置*时表示所有域名不允许cookie使用
        corsConfiguration.addAllowedOrigin("http://www.store.com"); //设置允许跨域域名，设置*时表示所有域名不允许cookie使用
        corsConfiguration.setAllowCredentials(true); //是否允许携带cookie信息
        corsConfiguration.addAllowedMethod("*"); //允许所有请求方式跨域
        corsConfiguration.addAllowedHeader("*"); //允许所有请求头跨域

        UrlBasedCorsConfigurationSource urlBasedCorsConfigurationSource = new UrlBasedCorsConfigurationSource(); //初始化配置源对象
        urlBasedCorsConfigurationSource.registerCorsConfiguration("/**", corsConfiguration); //拦截所有请求，校验是否允许跨域
        return new CorsFilter(urlBasedCorsConfigurationSource);
    }
}
```