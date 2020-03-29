---
author: IEvangelist
ms.author: dapine
ms.date: 06/25/2019
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: b9d0d2b97472eb3264f5e4600fddbfc7d3250918
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "67704197"
---
## <a name="use-the-docker-cli-to-authenticate-the-private-container-registry"></a>Аутентификация частного реестра контейнеров с помощью интерфейса командной строки Docker

Выполнить аутентификацию с частным реестром контейнеров для контейнеров Cognitive Services можно несколькими способами, но мы рекомендуем использовать командную строку [Docker CLI](https://docs.docker.com/engine/reference/commandline/cli/).

Используйте [ `docker login` команду,](https://docs.docker.com/engine/reference/commandline/login/)как показано в следующем примере, чтобы войти в `containerpreview.azurecr.io`частный реестр контейнеров для контейнеров Cognitive Services Containers. Замените * \<\> имя пользователя* именем пользователя и * \<паролем паролем\> * пароль, который содержится в учетных данных, полученных от группы Azure Cognitive Services.

```
docker login containerpreview.azurecr.io -u <username> -p <password>
```

Если вы сохранили свои учетные данные в текстовом файле, содержимое этого текстового файла можно сцепить с помощью команды `cat` в команду `docker login`, как показано в приведенном ниже примере. Замените * \<\> passwordFile* с траекторией и именем текстового файла, который содержит пароль и * \<имя\> пользователя* с именем пользователя, которое предусмотрено в ваших учетных данных.

```
cat <passwordFile> | docker login containerpreview.azurecr.io -u <username> --password-stdin
```
