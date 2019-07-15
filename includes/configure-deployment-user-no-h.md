---
title: включение файла
description: включение файла
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 06/14/2019
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: 4e699707db02de07f3d1ebb7d1fa8d0575a10aa3
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/11/2019
ms.locfileid: "67836907"
---
Для развертывания в веб-приложение Azure из FTP и локального репозитория Git можно использовать *пользователя развертывания*. Настроив один раз пользователя развертывания, вы сможете использовать его для всех последующих развертываний в Azure. Имя пользователя и пароль учетной записи развертывания отличаются от учетных данных подписки Azure. 

Чтобы настроить пользователя развертывания, выполните в Azure Cloud Shell команду [az webapp deployment user set](/cli/azure/webapp/deployment/user?view=azure-cli-latest#az-webapp-deployment-user-set). Вместо \<username> и \<password> укажите имя пользователя и пароль развертывания. 

- Имя пользователя должно быть уникальным в Azure. Кроме того, чтобы отправка в локальный репозиторий Git работала, имя пользователя не должно содержать символ @. 
- Пароль должен содержать не менее восьми символов и включать два из трех следующих элементов: буквы, цифры и символы. 

```azurecli-interactive
az webapp deployment user set --user-name <username> --password <password>
```

В выходных данных JSON пароль отображается как `null`. Если появляется сообщение об ошибке `'Conflict'. Details: 409`, измените имя пользователя. Если появляется сообщение об ошибке `'Bad Request'. Details: 400`, используйте более надежный пароль. 

Запишите имя пользователя и пароль и используйте их для развертывания веб-приложений.
