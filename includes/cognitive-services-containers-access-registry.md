---
author: IEvangelist
ms.author: dapine
ms.date: 06/25/2019
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: b9d0d2b97472eb3264f5e4600fddbfc7d3250918
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/09/2019
ms.locfileid: "67704197"
---
## <a name="use-the-docker-cli-to-authenticate-the-private-container-registry"></a>Аутентификация частного реестра контейнеров с помощью интерфейса командной строки Docker

Выполнить аутентификацию с частным реестром контейнеров для контейнеров Cognitive Services можно несколькими способами, но мы рекомендуем использовать командную строку [Docker CLI](https://docs.docker.com/engine/reference/commandline/cli/).

Используйте [команду `docker login`](https://docs.docker.com/engine/reference/commandline/login/), как показано в приведенном ниже примере, чтобы войти в частный реестр контейнеров Cognitive Services `containerpreview.azurecr.io`. Замените *\<username\>* на имя пользователя, а *\<password\>*  — на пароль из учетных данных, предоставленных командой Azure Cognitive Services.

```
docker login containerpreview.azurecr.io -u <username> -p <password>
```

Если вы сохранили свои учетные данные в текстовом файле, содержимое этого текстового файла можно сцепить с помощью команды `cat` в команду `docker login`, как показано в приведенном ниже примере. Замените *\<passwordFile\>* на путь и имя текстового файла, который содержит пароль, а *\<username\>*  — на имя пользователя из предоставленных учетных данных.

```
cat <passwordFile> | docker login containerpreview.azurecr.io -u <username> --password-stdin
```
