---
author: IEvangelist
ms.author: dapine
ms.service: cognitive-services
ms.topic: include
ms.date: 7/5/2019
ms.openlocfilehash: 993b0e8cc5b1ec482b2f6041dfc970dc7e7409dd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "68229292"
---
Заполните и отправьте форму запроса доступа к контейнеру [Cognitive Services Vision Containers.](https://aka.ms/VisionContainersPreview) В форме нужно указать сведения о себе, компании и пользовательском сценарии, для которого будет использоваться контейнер. После отправки формы команда Azure Cognitive Services проверяет ее, чтобы убедиться, что вы соответствуете критериям доступа к реестру частных контейнеров.

> [!IMPORTANT]
> В форме необходимо использовать адрес электронной почты, связанный с учетной записью Майкрософт (MSA) или учетной записью Azure Active Directory (Azure AD).

Если ваш запрос одобрен, вы получите электронное письмо с инструкциями, в которые описано, как получить ваши учетные данные и получить доступ к частному реестру контейнеров.

## <a name="log-in-to-the-private-container-registry"></a>Вход в частный реестр контейнеров

Существует несколько способов проверки подлинности с помощью частного реестра контейнеров для контейнеров Cognitive Services. Мы рекомендуем использовать метод командной строки с помощью [Docker CLI.](https://docs.docker.com/engine/reference/commandline/cli/)

Используйте команду [входа в докер,](https://docs.docker.com/engine/reference/commandline/login/) как показано в `containerpreview.azurecr.io`следующем примере, чтобы войти в, который является частным реестром контейнеров для контейнеров Cognitive Services. Замените * \<\> имя пользователя* именем пользователя и * \<паролем паролем,\> * предоставленным в учетных данных, полученных от группы Azure Cognitive Services.

```
docker login containerpreview.azurecr.io -u <username> -p <password>
```

Если вы закрепили свои учетные данные в текстовом файле, `docker login` вы можете совмещеть содержимое этого текстового файла с командой. Используйте `cat` команду, как показано в следующем примере. Замените * \<\> пароль* на путь и имя текстового файла, содержащего пароль. Замените * \<\> имя пользователя* именем пользователя, которое содержится в ваших учетных данных.

```
cat <passwordFile> | docker login containerpreview.azurecr.io -u <username> --password-stdin
```

