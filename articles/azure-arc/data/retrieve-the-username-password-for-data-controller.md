---
title: Получение имени пользователя и пароля для подключения к контроллеру данных Arc
description: Получение имени пользователя и пароля для подключения к контроллеру данных Arc
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 1c33cf21be0b4b1ea39a568d6df9fd90507dd454
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/22/2020
ms.locfileid: "90938958"
---
# <a name="retrieve-the-user-name-and-password-to-connect-to-the-arc-data-controller"></a>Получение имени пользователя и пароля для подключения к контроллеру данных Arc

Может возникнуть ситуация, когда необходимо получить имя пользователя и пароль для контроллера данных. Это команды, необходимые при запуске. 

```console
azdata login
```

Если вы являетесь администратором Kubernetes для кластера. Таким образом, у вас есть права на выполнение команд для получения из секрета Kubernetes, в котором хранятся сведения, хранящиеся в службе "Дуга Azure".

> [!NOTE]
>  Если вы использовали другое имя для пространства имен, в котором был создан контроллер данных, измените `-n arc` параметр в приведенных ниже командах, чтобы использовать имя пространства имен, в которое был создан контроллер данных.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="linux"></a>Linux

Выполните следующую команду, чтобы получить имя пользователя:

```console
kubectl -n arc get secret controller-login-secret -o=jsonpath="{.data['username']}" | base64 -d
```

Чтобы получить пароль, выполните следующую команду:

```console
kubectl -n arc get secret controller-login-secret -o=jsonpath="{.data['password']}" | base64 -d
```

## <a name="powershell"></a>PowerShell

Выполните следующую команду, чтобы получить имя пользователя:

```console
[Text.Encoding]::Utf8.GetString([Convert]::FromBase64String((kubectl -n arc get secret controller-login-secret -o=jsonpath="{.data['username']}")))
```

Чтобы получить пароль, выполните следующую команду:

```console
[Text.Encoding]::Utf8.GetString([Convert]::FromBase64String((kubectl -n arc get secret controller-login-secret -o=jsonpath="{.data['password']}")))
```

## <a name="next-steps"></a>Дальнейшие действия

Попробуйте другие [сценарии](https://github.com/microsoft/Azure-data-services-on-Azure-Arc/tree/master/scenarios)
