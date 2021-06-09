---
title: springboot学习之RESTful
date: 2017-07-03 19:09:56
categories: springboot
---
在学习HelloWorld之后，我们来尝试构建一个RESTful工程。
+ 什么是REST?
首先，REST是Web自身的架构风格。REST也是Web之所以取得成功的技术架构方面因素的总结。REST是世界上最成功的分布式应用架构风格（成功案例：Web，还不够吗？）。它是为 运行在互联网环境 的 分布式 超媒体系统量身定制的。互联网环境与企业内网环境有非常大的差别，最主要的差别是两个方面：
可伸缩性需求无法控制：并发访问量可能会暴涨，也可能会暴跌。
安全性需求无法控制：无法控制客户端发来的请求的格式，很可能会是恶意的请求。
而所谓的“超媒体系统”，即，使用了超文本的系统。可以把“超媒体”理解为超文本+媒体内容。
REST是HTTP/1.1协议等Web规范的设计指导原则，HTTP/1.1协议正是为实现REST风格的架构而设计的。新的Web规范，其设计必须符合REST的要求，否则整个Web的体系架构会因为引入严重矛盾而崩溃。这句话不是危言耸听，做个类比，假如苏州市政府同意在市区著名园林的附近大型土木，建造大量具有后现代风格的摩天大楼，那么不久之后世界闻名的苏州园林美景将不复存在。
上述这些关于“REST是什么”的描述，可以总结为一句话：REST是所有Web应用都应该遵守的架构设计指导原则。当然，REST并不是法律，违反了REST的指导原则，仍然能够实现应用的功能。但是违反了REST的指导原则，会付出很多代价，特别是对于大流量的网站而言。
<!--more-->
+ REST的关键词
 1. 资源（Resource）
 2. 资源的表述（Representation）
 3. 状态转移（State Transfer）
 4. 统一接口（Uniform Interface）
 5. 超文本驱动（Hypertext Driven）
 

+ REST的特征
 1. 面向资源（Resource Oriented）
 2. 可寻址（Addressability）
 3. 连通性（Connectedness）
 4. 无状态（Statelessness）
 5. 统一接口（Uniform Interface）
 6. 超文本驱动（Hypertext Driven）

更多可以参考：[理解本真的REST架构风格](http://www.infoq.com/cn/articles/understanding-restful-style)

+ 开始创建RESTful工程
  我们依旧使用IDEA作为开发工具，首先依旧是新建一个SpringInitializr项目，这里和前面的HelloWorld程序一样，但是在勾选Dependensencise时需要额外勾上MySQL和MyBaties.
  ![勾选Dependensencise](http://oo3aq3ac8.bkt.clouddn.com/007.png)
+ 创建数据库
  ```sql
DROP TABLE IF EXISTS `city`;

CREATE TABLE `city` (
  `id` int(10) unsigned NOT NULL AUTO_INCREMENT COMMENT '城市编号',
  `province_id` int(10) unsigned NOT NULL COMMENT '省份编号',
  `city_name` varchar(25) DEFAULT NULL COMMENT '城市名称',
  `description` varchar(25) DEFAULT NULL COMMENT '描述',
  PRIMARY KEY (`id`)
) ENGINE=InnoDB AUTO_INCREMENT=4 DEFAULT CHARSET=utf8;
  ```

+ 创建工程目录如下
![创建工程目录](http://oo3aq3ac8.bkt.clouddn.com/008.png)

Controller包里存放控制器：
```java
package org.vzard.springboot.controller;

import jdk.nashorn.internal.ir.RuntimeNode;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.*;
import org.vzard.springboot.domain.City;
import org.vzard.springboot.service.CityService;

import java.util.List;

/**
 * Created by V-zar on 2017/7/2.
 */


@RestController
public class CityRestController {

    @Autowired
    private CityService cityService;

    

    @RequestMapping(value = "/vzard/city/{id}",method = RequestMethod.GET)
    public City findOneCity(@PathVariable Long id){
        return cityService.findCityById(id);
    }

    @RequestMapping(value = "/vzard/city",method = RequestMethod.GET)
    public List<City> findAllCity(){
        return cityService.findAllCity();
    }

    @RequestMapping(value = "/vzard/city",method = RequestMethod.POST)
    public void createCity(@RequestBody City city){
        cityService.saveCity(city);
    }

    @RequestMapping(value = "/vzard/city",method = RequestMethod.PUT)
    public void modifyCity(@RequestBody City city){
        cityService.updateCity(city);
    }

    @RequestMapping(value = "/vzard/city/{id}",method = RequestMethod.DELETE)
    public void modifyCity(@PathVariable("id") Long id){
        cityService.deleteCity(id);
    }


}

```

dao包存放接口类：
```java
package org.vzard.springboot.dao;

import org.apache.ibatis.annotations.Param;
import org.vzard.springboot.domain.City;

import java.util.List;

/**
 * 城市DAO接口类
 *
 * Created by V-zar on 2017/7/2.
 */
public interface CityDao {

    /**
     * 获取城市信息列表
     * @return
     */
    List<City> findAllCity();

    /**
     * 根据id获取城市信息
     * @param id
     * @return
     */
    City findById(@Param("id") Long id);

    /**
     * 保存城市信息
     * @param city
     * @return
     */
    Long saveCity(City city);

    /**
     * 更新城市信息
     * @param city
     * @return
     */
    Long updateCity(City city);

    /**
     * 删除城市信息
     * @param id
     * @return
     */
    Long deleteCity(Long id);



}

```

domain包存放实体类
```java
package org.vzard.springboot.domain;

/**
 * 城市实体类
 *
 * Created by V-zar on 2017/7/2.
 */
public class City {

    /**
     * 城市id
     */
    private Long id;
    /**
     * 省份id
     */
    private Long provinceId;
    /**
     * 城市名称
     */
    private String cityName;
    /**
     * 描述
     */
    private String description;

    public Long getId() {
        return id;
    }

    public void setId(Long id) {
        this.id = id;
    }

    public Long getProvinceId() {
        return provinceId;
    }

    public void setProvinceId(Long provinceId) {
        this.provinceId = provinceId;
    }

    public String getCityName() {
        return cityName;
    }

    public void setCityName(String cityName) {
        this.cityName = cityName;
    }

    public String getDescription() {
        return description;
    }

    public void setDescription(String description) {
        this.description = description;
    }
}
```

service包里存放服务接口及其实现
```java
package org.vzard.springboot.service;

import org.vzard.springboot.domain.City;

import java.util.List;

/**
 * 城市业务逻辑接口
 *
 * Created by V-zar on 2017/7/2.
 */
public interface CityService {

    /**
     * 获取城市列表
     * @return
     */
    List<City> findAllCity();

    /**
     * 根据城市id寻找城市
     * @param id
     * @return
     */
    City findCityById(Long id);

    /**
     * 保存城市信息
     * @param city
     * @return
     */
    Long saveCity(City city);

    /**
     * 更新城市信息
     * @param city
     * @return
     */
    Long updateCity(City city);

    /**
     * 删除城市信息
     * @param id
     * @return
     */
    Long deleteCity(Long id);

}


```

```java
package org.vzard.springboot.service.impl;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;
import org.vzard.springboot.dao.CityDao;
import org.vzard.springboot.domain.City;
import org.vzard.springboot.service.CityService;

import java.util.List;

/**
 * Created by V-zar on 2017/7/2.
 */
@Service
public class CityServiceImpl implements CityService{

    @Autowired
    private CityDao cityDao;

    @Override
    public List<City> findAllCity() {
        return cityDao.findAllCity();
    }

    @Override
    public City findCityById(Long id) {
        return cityDao.findById(id);
    }

    @Override
    public Long saveCity(City city) {
        return cityDao.saveCity(city);
    }

    @Override
    public Long updateCity(City city) {
        return cityDao.updateCity(city);
    }

    @Override
    public Long deleteCity(Long id) {
        return cityDao.deleteCity(id);
    }
}

```

程序入口类
```java
package org.vzard.springboot;

import org.mybatis.spring.annotation.MapperScan;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
@MapperScan("org.vzard.springboot.dao")
public class SpringbootApplication {

    public static void main(String[] args) {
        SpringApplication.run(SpringbootApplication.class, args);
    }
}

```

数据源&MyBaties&端口配置
```
#数据源配置
spring.datasource.url=jdbc:mysql://localhost:3306/springbootdb?useUnicode=true&characterEncoding=utf8
spring.datasource.username=root
spring.datasource.password=5432
spring.datasource.dbcp2.driver-class-name=com.mysql.jdbc.Driver

#Mybatis 配置
mybatis.typeAliasesPackage=org.vzard.springboot.domain
mybatis.mapperLocations=classpath:mapper/*.xml

#配置端口
server.port=${port:8009}
```

Mybaties映射文件
```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" "http://mybatis.org/dtd/mybatis-3-mapper.dtd" >
<mapper namespace="org.vzard.springboot.dao.CityDao">
    <resultMap id="BaseResultMap" type="org.vzard.springboot.domain.City">
        <result column="id" property="id" />
        <result column="province_id" property="provinceId" />
        <result column="city_name" property="cityName" />
        <result column="description" property="description" />
    </resultMap>

    <parameterMap id="City" type="org.vzard.springboot.domain.City"/>

    <sql id="Base_Column_List">
        id, province_id, city_name, description
    </sql>

    <select id="findById" resultMap="BaseResultMap" parameterType="java.lang.Long">
        select
        <include refid="Base_Column_List" />
        from city
        where id = #{id}
    </select>

    <select id="findAllCity" resultMap="BaseResultMap" >
        select
        <include refid="Base_Column_List" />
        from city
    </select>

    <insert id="saveCity" parameterMap="City" useGeneratedKeys="true" keyProperty="id">
        insert into
        city(id,province_id,city_name,description)
        values
        (#{id},#{provinceId},#{cityName},#{description})
    </insert>

    <update id="updateCity" parameterMap="City">
        update
        city
        set
        <if test="provinceId!=null">
            province_id = #{provinceId},
        </if>
        <if test="cityName!=null">
            city_name = #{cityName},
        </if>
        <if test="description!=null">
            description = #{description}
        </if>
        where
        id = #{id}
    </update>

    <delete id="deleteCity" parameterType="java.lang.Long">
        delete from
        city
        where
        id = #{id}
    </delete>
</mapper>

```

到此工程创建完毕运行程序入口类启动项目：
+ 插入记录，URL：http://127.0.0.1:8009/vzard/city
![插入记录](http://oo3aq3ac8.bkt.clouddn.com/009.png)

+ 更改记录，URL:http://127.0.0.1:8009/vzard/city
![更改记录](http://oo3aq3ac8.bkt.clouddn.com/010.png)

+ 删除记录，URL:http://127.0.0.1:8009/vzard/city/2
![删除记录](http://oo3aq3ac8.bkt.clouddn.com/014.png)

+ 查询记录
![查询记录](http://oo3aq3ac8.bkt.clouddn.com/013.png)


*表单提交的测试工具为Chrome的插件PostMan,很强大*