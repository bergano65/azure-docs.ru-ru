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
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60850217"
---
Чтобы открыть прямой сеанс SSH с контейнером, необходимо запустить приложение.

Вставьте следующий URL-адрес в браузер и замените \<app_name> именем вашего приложения:

```
https://<app-name>.scm.azurewebsites.net/webssh/host
```

Если вы не прошли аутентификацию, это потребуется сделать, подключившись с помощью подписки Azure. После выполнения проверки подлинности отобразится оболочка в браузере. Здесь можно запускать команды внутри контейнера.

![SSL-подключение](./media/app-service-web-ssh-connect-no-h/app-service-linux-ssh-connection.png)
