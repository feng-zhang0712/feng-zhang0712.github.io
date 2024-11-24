---
layout: post
title: 《Web API 教程 - Geolocation API》摘录
categories: blog
---

> 本文是对[《Web API 教程 - Geolocation API》](https://wangdoc.com/webapi/geolocation)一章中知识点的摘录，请访问原文，获取更多详细信息。

Geolocation API 用于获取用户的地理位置。

由于该功能涉及用户隐私，所以浏览器会提示用户，是否同意给出地理位置，用户可能会拒绝。另外，这个 API 只能在 HTTPS 环境使用。

浏览器通过 `navigator.geolocation` 属性提供该 API。

## 一、Geolocation 对象

`navigator.geolocation` 属性返回一个 Geolocation 对象。该对象具有以下三个方法。

- `Geolocation.getCurrentPosition(success, error, options)`：返回一个 Position 对象，表示用户的当前位置。
- `Geolocation.watchPosition(success, error, options)`：指定一个监听函数，每当用户的位置发生变化，就执行该监听函数。
- `Geolocation.clearWatch(id)`：取消 `watchPosition()` 指定的监听函数。

### 1.1 Geolocation.getCurrentPosition()

`navigator.geolocation.getCurrentPosition(success, error, options)` 用于获取用户的位置。

- `success`：用户同意给出位置时的回调函数，它的参数是一个 Position 对象。
- `error`：用户拒绝给出位置时的回调函数，它的参数是一个 PositionError 对象。该参数可选。
- `options`：参数对象，该参数可选。

Position 对象有两个属性。

- `Position.coords`：返回一个 Coordinates 对象，表示当前位置的坐标。
- `Position.timestamp`：返回一个对象，代表当前时间戳。

PositionError 对象主要有两个属性。

- `PositionError.code`：整数，表示发生错误的原因。`1` 表示无权限，有可能是用户拒绝授权；`2` 表示无法获得位置，可能设备有故障；`3` 表示超时。
- `PositionError.message`：字符串，表示错误的描述。

参数对象 `option` 可以指定三个属性。

- `enableHighAccuracy`：布尔值，是否返回高精度结果。如果设为 `true`，可能导致响应时间变慢或（移动设备的）功耗增加；反之，如果设为 `false`，设备可以更快速地响应。默认值为 `false`。
- `timeout`：正整数，表示等待查询的最长时间，单位为毫秒。默认值为 `Infinity`。
- `maximumAge`：正整数，表示可接受的缓存最长时间，单位为毫秒。如果设为 `0`，表示不返回缓存值，必须查询当前的实际位置；如果设为 `Infinity`，必须返回缓存值，不管缓存了多少时间。默认值为 `0`。

### 1.2 Geolocation.watchPosition()

`navigator.geolocation.watchPosition(success, error, options)`

- `success`：监听成功的回调函数，该函数的参数为一个 Position 对象。
- `error`：该参数可选，表示监听失败的回调函数，该函数的参数是一个 PositionError 对象。
- `options`：该参数可选，表示监听的参数配置对象。

该方法返回一个整数值，表示监听函数的编号。该整数用来供 `Geolocation.clearWatch(id)` 取消监听。

### 1.3 Geolocation.clearWatch()

`navigator.geolocation.clearWatch(id)` 用来取消 `watchPosition()` 指定的监听函数。它的参数是 `watchPosition()` 返回的监听函数的编号。

## 二、Coordinates 对象

Coordinates 对象是地理位置的坐标接口，`Position.coords` 属性返回的就是这个对象。

它有以下属性，全部为只读属性。

- `Coordinates.latitude`：浮点数，表示纬度。
- `Coordinates.longitude`：浮点数，表示经度。
- `Coordinates.altitude`：浮点数，表示海拔（单位：米）。
- `Coordinates.accuracy`：浮点数，表示经度和纬度的精度（单位：米）。
- `Coordinates.altitudeAccuracy`：浮点数，表示海拔的精度（单位：米）。
- `Coordinates.speed`：浮点数，表示设备的速度（单位：米/秒）。
- `Coordinates.heading`：浮点数，表示设备前进的方向（单位：度）。方向按照顺时针，北方是 `0` 度，东方是 `90` 度，西方是 `270` 度。如果 `Coordinates.speed` 为 `0`，`heading` 属性返回 `NaN`。

## 三、参考

- 阮一峰，[Web API 教程 - Geolocation API](https://wangdoc.com/webapi/geolocation)