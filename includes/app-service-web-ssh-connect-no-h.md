---
title: включить файл
description: включить файл
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 03/29/2019
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: 458cd36a35ea37b2a317fe98fdeb5acc69a36ce8
ms.sourcegitcommit: d2222681e14700bdd65baef97de223fa91c22c55
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/07/2020
ms.locfileid: "91822815"
---
Чтобы открыть прямой сеанс SSH с контейнером, необходимо запустить приложение.

Вставьте следующий URL-адрес в браузер и замените `<app-name>` именем вашего приложения:

```
https://<app-name>.scm.azurewebsites.net/webssh/host
```

Если вы не прошли аутентификацию, это потребуется сделать, подключившись с помощью подписки Azure. После выполнения проверки подлинности отобразится оболочка в браузере. Здесь можно запускать команды внутри контейнера.

![SSL-подключение](./media/app-service-web-ssh-connect-no-h/app-service-linux-ssh-connection.png)
