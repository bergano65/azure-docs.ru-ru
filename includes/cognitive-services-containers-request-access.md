---
author: diberry
ms.author: v-junlch
ms.service: cognitive-services
ms.topic: include
origin.date: 01/24/2019
ms.date: 02/21/2019
ms.openlocfilehash: 11a336bbcf75c6c4de61f1bb681ab6ee7aa05650
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60815628"
---
Прежде всего необходимо заполнить и отправить [форму запроса к контейнерам Cognitive Services Vision](https://aka.ms/VisionContainersPreview), чтобы запросить доступ к контейнеру. В форме нужно указать сведения о себе, компании и пользовательском сценарии, для которого будет использоваться контейнер. Когда вы отправите форму, команда Azure Cognitive Services просмотрит ее, чтобы проверить, соблюдаются ли критерии доступа к частному реестру контейнеров.

> [!IMPORTANT]
> В форме необходимо указать электронный адрес, связанный с учетной записью Майкрософт или учетной записью Azure Active Directory (Azure AD).

Если ваш запрос будет утвержден, вы получите по электронной почте инструкции по получению учетных данных и доступа к частному реестру контейнеров.

## <a name="log-in-to-the-private-container-registry"></a>Вход в частный реестр контейнеров

Выполнить проверку подлинности с частным реестром контейнеров для контейнеров Cognitive Services можно несколькими способами, но мы рекомендуем использовать командную строку [Docker CLI](https://docs.docker.com/engine/reference/commandline/cli/).

Используйте команду [docker login](https://docs.docker.com/engine/reference/commandline/login/), как показано в приведенном ниже примере, чтобы войти в `containerpreview.azurecr.io` — частный реестр контейнеров Cognitive Services. Замените *\<username\>* на имя пользователя, а *\<password\>* — на пароль, предоставленные в учетных данных, полученных от команды Azure Cognitive Services.

```
docker login containerpreview.azurecr.io -u <username> -p <password>
```

Если вы сохранили свои учетные данные в текстовом файле, содержимое этого текстового файла можно сцепить с помощью команды `cat` в команду `docker login`, как показано в приведенном ниже примере. Замените *\<passwordFile\>* на путь и имя текстового файла, который содержит пароль, а *\<username\>* — на имя пользователя, предоставленное в учетных данных.

```
cat <passwordFile> | docker login containerpreview.azurecr.io -u <username> --password-stdin
```


<!-- ms.date: 02/21/2019 -->