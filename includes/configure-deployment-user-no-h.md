---
title: включить файл
description: включить файл
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 06/14/2019
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: 4ceae4e7e2d10c80a929a4a822c877da8d8478f0
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/06/2020
ms.locfileid: "96748211"
---
Для развертывания в веб-приложение Azure из FTP и локального репозитория Git можно использовать *пользователя развертывания*. Настроив один раз пользователя развертывания, вы сможете использовать его для всех последующих развертываний в Azure. Имя пользователя и пароль учетной записи развертывания отличаются от учетных данных подписки Azure. 

Чтобы настроить пользователя развертывания, выполните в Azure Cloud Shell команду [az webapp deployment user set](/cli/azure/webapp/deployment/user?view=azure-cli-latest#az-webapp-deployment-user-set). Вместо \<username> и \<password> укажите имя пользователя и пароль развертывания. 

- Имя пользователя должно быть уникальным в Azure. Кроме того, чтобы отправка в локальный репозиторий Git работала, имя пользователя не должно содержать символ "\@". 
- Пароль должен содержать не менее восьми символов и включать два из трех следующих элементов: буквы, цифры и символы. 

```azurecli-interactive
az webapp deployment user set --user-name <username> --password <password>
```

В выходных данных JSON пароль отображается как `null`. Если появляется сообщение об ошибке `'Conflict'. Details: 409`, измените имя пользователя. Если появляется сообщение об ошибке `'Bad Request'. Details: 400`, используйте более надежный пароль. 

Запишите имя пользователя и пароль и используйте их для развертывания веб-приложений.
