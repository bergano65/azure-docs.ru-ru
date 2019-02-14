---
author: diberry
ms.author: diberry
ms.service: cognitive-services
ms.topic: include
ms.date: 01/24/2019
ms.openlocfilehash: d5a6692aceedb6dc7fad7a434a433882cdbea015
ms.sourcegitcommit: 943af92555ba640288464c11d84e01da948db5c0
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/09/2019
ms.locfileid: "55984911"
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

