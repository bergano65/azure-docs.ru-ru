---
title: включение файла
description: включение файла
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 03/29/2019
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: 060bc1039982cc0a77214d5dbe2a08de7a839c84
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/13/2019
ms.locfileid: "59550014"
---
Чтобы открыть прямого сеанса SSH с контейнером, приложения должна быть запущена.

Вставьте следующий URL-адрес в браузере и заменить \<app-name > именем своего приложения:

```
https://<app-name>.scm.azurewebsites.net/webssh/host
```

Если вы все еще не прошел проверку подлинности, вы должны проходить проверку подлинности с вашей подпиской Azure для подключения. После выполнения проверки подлинности отобразится оболочка в браузере. Здесь можно запускать команды внутри контейнера.

![SSL-подключение](./media/app-service-web-ssh-connect-no-h/app-service-linux-ssh-connection.png)
