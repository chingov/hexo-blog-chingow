---
title: smartcity框架 -- 04.接口说明
copyright: true
related_posts: true
categories: 框架
tags:
  - smartcity
  - 框架
abbrlink: 79afe3c1
date: 2018-04-18 15:11:31
---
### 工具接口说明
#### DateUtil
时间转换工具类（com.xxx.core.util.DateUtil）

* 字段概要

| 限定符 | 类型 | 字段 | 说明 |
| :---: | --- | --- | --- |
| static | String	| DATE_FORMAT_HH_MM | HH:mm |
| static | String	| DATE_FORMAT_YYYY_MM_DD | yyyy-MM-dd |
| static | String	| DATE_FORMAT_YYYY_MM_DD_HH_MM | yyyy-MM-dd HH:mm |
| static | String	| DATE_FORMAT_YYYY_MM_DD_HH_MM_SS | yyyy-MM-dd HH:mm:ss |
| static | String	| DATE_FORMAT_YYYYMMDD | yyyyMMdd |
| static | String	| DATE_FORMAT_YYYYMMDDHHMMSS | yyyyMMddHHmmss |
<!-- more -->

* 方法概要

| 返回类型 | 方法 | 说明
| --- | --- |--- |
| String | dateToDateStr(java.util.Date date) | Date转换为日期字符串:yyyy-MM-dd
| String | dateToSimpleTimeStr(java.util.Date date)|Date转换为时间字符串:yyyy-MM-dd HH:mm
| String | dateToStr(java.util.Date date, java.text.SimpleDateFormat sdf) | 时间转换为字符串:sdf
| String | dateToTimeStr(java.util.Date date) | Date转换为时间字符串:yyyy-MM-dd HH:mm:ss
| String | dateToTimeXXStr(java.util.Date date) | Date转换为时间字符串:yyyyMMddHHmmss
| String | formatDate(java.util.Date date, java.lang.String format) | 日期转换,自定义格式
| String | formatDateStringWithDot(java.lang.String timeStr) | 处理从数据库查询的日期串带 .0的情况
| Date | formatIdentifyIdToBirthday(java.lang.String identifyId) | 将身份证转换为生日Date
| String | friendlyFormat(java.util.Date date) | 友好的方式显示时间 , 默认不是当天显示 yyyy-MM-dd HH:mm
| String | friendlyFormat(java.util.Date date, java.lang.String pattern) | 友好的方式显示时间
| String | friendlyFormat(long dateLong) | 友好的方式显示时间 , 默认不是当天显示 yyyy-MM-dd HH:mm
| String	 | friendlyFormat(long dateLong, java.lang.String pattern) | 友好的方式显示时间
| String | friendlyFormat(java.lang.String str) | 友好的方式显示时间 , 默认不是当天显示 yyyy-MM-dd HH:mm
| Date | getAfterDays(java.util.Date date, int days) | 获取某日期之后X天的日期
| Date | getBeforeDays(java.util.Date date, int days) | 获取某日期之前X天的日期
| Date | getDate() | 获取当前时间:Date
| int[] | getDatetimeArray(java.util.Date date) | 获取某时间的 年月日时分秒
| List | getMonthDays(int year, int month, java.lang.String format) | 获取指定年月的所有天数的字符串集合
| String | getWeek(java.lang.String dateStr) | 判断一个日期是星期几
| Integer | getWeekDay(java.lang.String dateStr) | 根据时间格式获取时间当前星期
| boolean | isBeForeNow(java.lang.String formatDate) | 判断时间是否是当前时间之前 ,当天不算
| boolean | isDate(java.lang.String dateStr) | 判断字符串是否为时间格式 
| Date | parseDate(java.lang.String dateStr, java.lang.String format) | 日期转换,自定义格式
| Date | strToDate(java.lang.String str) | 字符串转换为日期:yyyy-MM-dd
| Date | strToTime(java.lang.String str) | 字符串转换为时间: yyyy-MM-dd HH:mm:ss
| Date | strXXToTime(java.lang.String str) | 字符串转换为时间: yyyyMMddHHmmss
| String | timeStrToDateStr(java.lang.String str) | 字符串由时间格式转换为日期格式: yyyy-MM-dd HH:mm:ss → yyyy-MM-dd
| Integer | transferBirthdayToAge(java.util.Date birthday) | 根据时间得到年龄




