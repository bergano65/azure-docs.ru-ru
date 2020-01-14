---
title: включить файл
description: включить файл
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 02/02/2018
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: e5ba08da5c58ff486bc26f2c771dfef55452629a
ms.sourcegitcommit: a100e3d8b0697768e15cbec11242e3f4b0e156d3
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/06/2020
ms.locfileid: "75681065"
---
Вернитесь к окну терминала (в локальном расположении) и добавьте удаленное приложение Azure в локальный репозиторий Git. Замените *\<deploymentLocalGitUrl-from-create-step>* URL-адресом удаленного репозитория Git, который вы сохранили при [создании веб-приложения](#create-a-web-app).

```bash
git remote add azure <deploymentLocalGitUrl-from-create-step>
```

Отправьте код в удаленное приложение Azure, чтобы развернуть приложение. При появлении запроса на ввод учетных данных в диспетчере учетных данных Git введите учетные данные, созданные на шаге [настройки пользователя развертывания](/azure/app-service/containers/tutorial-python-postgresql-app#configure-a-deployment-user) (а не те, которые используются для входа на портал Azure).

```bash
git push azure master
```

Выполнение этой команды может занять несколько минут. При выполнении эта команда выводит приблизительно следующие сведения:
