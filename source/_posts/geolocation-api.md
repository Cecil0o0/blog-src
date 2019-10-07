---
title: Geolocation API
tags:
  - Geolocation
  - web APIs
url: 292.html
id: 292
categories:
  - note
  - technology
date: 2017-08-29 02:42:49
---

what? web地理位置API how? 判断地理位置API是否可用

    if ('geolocation' in navigator) {
    
    } else {
    
    }
    

获取当前位置

    navigator.geolocation.getCurrentPosition(function(position) {
        do_something(position.coords.latitude, position.coords.longitude);
    }, function(err){ handle(err) });
    

监视位置变化或清除变化

    var watchID = navigator.geolocation.watchPosition(function(position) {
      do_something(position.coords.latitude, position.coords.longitude);
    }, function(err) { handle(error) });
    navigator.geolocation.clearWatch(watchID)
    

可选选项

    var geo_options = {
      enableHighAccuracy  : true,
      maximumAge            : 30000,
      timeout                    : 27000
    };
    var wpid = navigator.geolocation.watchPosition(geo_success, geo_error, geo_options);