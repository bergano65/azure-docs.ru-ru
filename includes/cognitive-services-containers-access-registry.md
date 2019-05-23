---
author: diberry
ms.author: diberry
ms.service: cognitive-services
ms.topic: include
ms.date: 05/07/2019
ms.openlocfilehash: 27c6b59a458fb79b86d7064d710a01593a1745dc
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/23/2019
ms.locfileid: "66125136"
---
## <a name="use-docker-cli-to-authenticate-private-container-registry"></a>Аутентификация частного реестра контейнеров с помощью интерфейса командной строки Docker

Выполнить проверку подлинности с частным реестром контейнеров для контейнеров Cognitive Services можно несколькими способами, но мы рекомендуем использовать командную строку [Docker CLI](https://docs.docker.com/engine/reference/commandline/cli/).

Используйте команду [docker login](https://docs.docker.com/engine/reference/commandline/login/), как показано в приведенном ниже примере, чтобы войти в `containerpreview.azurecr.io` — частный реестр контейнеров Cognitive Services. Замените *\<username\>* на имя пользователя, а *\<password\>* — на пароль, предоставленные в учетных данных, полученных от команды Azure Cognitive Services.

```
docker login containerpreview.azurecr.io -u <username> -p <password>
```

Если вы сохранили свои учетные данные в текстовом файле, содержимое этого текстового файла можно сцепить с помощью команды `cat` в команду `docker login`, как показано в приведенном ниже примере. Замените *\<passwordFile\>* на путь и имя текстового файла, который содержит пароль, а *\<username\>* — на имя пользователя, предоставленное в учетных данных.

```
cat <passwordFile> | docker login containerpreview.azurecr.io -u <username> --password-stdin
```
