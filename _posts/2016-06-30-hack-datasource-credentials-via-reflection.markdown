---
layout:     post
title:      "利用反射获得数据源连接信息"
subtitle:   ""
date:       2016-06-30
author:     "lixuanbin"
header-img: "img/bg11.jpg"
tags:
    - 后端开发
    - Java
---

   在生产环境中MySQL以云插件的方式提供给应用，连接凭据由运维掌控而对应用开发者屏蔽，在需要的时候难免有些不便。这时候我们可以采用反射的方式还原出来，代码如下：

```java
import java.lang.reflect.Field;

import org.apache.log4j.Logger;

public class CrackDbInfo {
	protected static final Logger log = Logger.getLogger(CrackDbInfo.class);

	/**
	 * 利用反射获取数据源连接信息
	 * 
	 * @param dataSource
	 * @return
	 */
	public static String retriveCredentials(javax.sql.DataSource dataSource) {
		String result = null;
		try {
			Field host = dataSource.getClass().getDeclaredField("host");
			host.setAccessible(true);
			Object hostValue = host.get(dataSource);
			Field port = dataSource.getClass().getDeclaredField("port");
			port.setAccessible(true);
			Object portValue = port.get(dataSource);
			Field database = dataSource.getClass().getDeclaredField("database");
			database.setAccessible(true);
			Object databaseValue = database.get(dataSource);
			Field user = dataSource.getClass().getDeclaredField("user");
			user.setAccessible(true);
			Object userValue = user.get(dataSource);
			Field pwd = dataSource.getClass().getDeclaredField("password");
			pwd.setAccessible(true);
			Object pwdValue = pwd.get(dataSource);
			result = String
					.format("{\"user\": \"%s\", \"pwd\": \"%s\", \"host\": \"%s\", \"port\": %s, \"database\": \"%s\"}",
							userValue.toString(), pwdValue.toString(), hostValue.toString(),
							portValue.toString(), databaseValue.toString());
		} catch (NoSuchFieldException e) {
			log.error(e.getMessage(), e);
		} catch (SecurityException e) {
			log.error(e.getMessage(), e);
		} catch (IllegalArgumentException e) {
			log.error(e.getMessage(), e);
		} catch (IllegalAccessException e) {
			log.error(e.getMessage(), e);
		}
		return result;
	}
}
```