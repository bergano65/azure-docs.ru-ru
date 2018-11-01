---
title: включение файла
description: включение файла
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 02/02/2018
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: b5512ed5810ebd05596dc3ca7a29957b4c595c59
ms.sourcegitcommit: f6050791e910c22bd3c749c6d0f09b1ba8fccf0c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50035311"
---
В Cloud Shell создайте учетные данные развертывания с помощью команды [`az webapp deployment user set`](/cli/azure/webapp/deployment/user?view=azure-cli-latest#az-webapp-deployment-user-set). Пользователь развертывания необходим для развертывания FTP и локального репозитория Git в веб-приложении. Имя пользователя и пароль представляют уровень учетной записи. _Они отличаются от учетных данных подписки Azure._

В следующем примере замените *\<имя_пользователя>* и *\<пароль>* (включая скобки) новым именем пользователя и паролем. Имя пользователя должно быть уникальным в пределах Azure. Пароль должен содержать не менее восьми символов и включать два из трех следующих элементов: буквы, цифры и символы. 

```azurecli-interactive
az webapp deployment user set --user-name <username> --password <password>
```

Необходимо получить выходные данные JSON с паролем, обозначенным как `null`. Если появляется сообщение об ошибке `'Conflict'. Details: 409`, измените имя пользователя. Если появляется сообщение об ошибке ` 'Bad Request'. Details: 400`, используйте более надежный пароль.

Пользователь развертывания создается один раз. Его можно использовать для всех развертываний Azure.

> [!NOTE]
> Запишите имя пользователя и пароль. В дальнейшем они понадобятся для развертывания веб-приложения.
>
>
