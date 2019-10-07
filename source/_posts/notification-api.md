---
title: Notification API
tags:
  - Notification
  - Permission
  - web APIs
url: 257.html
id: 257
categories:
  - note
  - technology
date: 2017-08-28 09:16:01
---

what? desktop-notification API why? 原声 alert 交互体验差 how? 申请 notification 权限

    window.addEventListener('load', function(){
        if (window.Notification && Notification.permission !== 'granted') {
            Notification.requestPermission(function () {
            if (Notification.permission !== status) {
                Notification.perssion = status
            }
            })
        }
    })

触发通知

    function notify(msg) {
        if (window.Notification && Notification.permission === 'granted') {
            var n = new Notification(msg)
        }
         // 如果用户没有选择是否显示通知
        // 注：因为在 Chrome 中我们无法确定 permission 属性是否有值，因此
        // 检查该属性的值是否是 "default" 是不安全的。
        else if (window.Notification && Notification.permission !== "denied") {
          Notification.requestPermission(function (status) {
            if (Notification.permission !== status) {
              Notification.permission = status
            }

            // 如果用户同意了
            if (status === "granted") {
              var n = new Notification(msg)
            }

            // 否则，我们可以让步的使用常规模态的 alert
            else {
              alert(msg)
            }
          });
        }
        // 用户拒绝通知
        else {
            alert(msg)
        }
    }
