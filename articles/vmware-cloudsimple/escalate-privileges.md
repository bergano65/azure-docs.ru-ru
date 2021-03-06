---
title: Решение Azure VMware с помощью Клаудсимпле — эскалация прав Клаудсимпле
description: В этой статье описывается, как эскалировать разрешения Клаудсимпле для выполнения административных функций в частном облаке vCenter.
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 524772578ad724e969bbeab0be0a3edcf32a845f
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/19/2019
ms.locfileid: "69619616"
---
# <a name="escalate-cloudsimple-privileges-to-perform-administrative-functions-in-private-cloud-vcenter"></a>Эскалация привилегий Клаудсимпле для выполнения административных функций в частном облаке vCenter

Подход с привилегиями Клаудсимпле предназначен для предоставления пользователям vCenter прав, необходимых для выполнения обычных операций. В некоторых случаях пользователю могут потребоваться дополнительные привилегии для выполнения определенной задачи.  Вы можете повышать привилегии пользователя службы единого входа vCenter в течение ограниченного периода времени.

Причины для повышения привилегий могут включать в себя следующее:

* Настройка источников удостоверений
* Управление пользователями
* Удаление группы распределенных портов
* Установка решений vCenter (например, приложения резервного копирования)
* Создание учетных записей служб

> [!WARNING]
> Действия, выполненные в повышенном привилегированном состоянии, могут негативно повлиять на систему и привести к недоступности системы. Выполнение только необходимых действий в течение периода эскалации.

На портале Клаудсимпле передайте [права](escalate-private-cloud-privileges.md) локального пользователя клаудовнер на сервер службы vCenter SSO.  Права удаленного пользователя можно эскалировать, только если в vCenter настроен дополнительный поставщик удостоверений.  Эскалация привилегий подразумевает Добавление выбранного пользователя в группу встроенных администраторов vSphere.  Только один пользователь может иметь повышенные привилегии.  Если необходимо эскалировать привилегии другого пользователя, сначала следует разоактивировать привилегии текущих пользователей.

Пользователи из дополнительных источников удостоверений должны быть добавлены как члены группы Клаудовнер.

В течение периода эскалации Клаудсимпле использует автоматическое наблюдение с соответствующими уведомлениями об оповещениях для обнаружения непреднамеренного изменения среды.
