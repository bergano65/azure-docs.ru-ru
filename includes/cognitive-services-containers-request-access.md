---
author: IEvangelist
ms.author: dapine
ms.service: cognitive-services
ms.topic: include
ms.date: 7/5/2019
ms.openlocfilehash: 993b0e8cc5b1ec482b2f6041dfc970dc7e7409dd
ms.sourcegitcommit: 920ad23613a9504212aac2bfbd24a7c3de15d549
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/15/2019
ms.locfileid: "68229292"
---
Заполнение и отправление [форму запроса контейнеры компьютерного зрения Cognitive Services](https://aka.ms/VisionContainersPreview) чтобы запросить доступ к контейнеру. В форме нужно указать сведения о себе, компании и пользовательском сценарии, для которого будет использоваться контейнер. После отправки формы, команда Azure Cognitive Services рассматриваются его, чтобы убедиться в том, что выполнены условия доступа для закрытого реестра контейнеров.

> [!IMPORTANT]
> Необходимо использовать адрес электронной почты, связанным с учетной записью Майкрософт (MSA) или учетной записи Azure Active Directory (Azure AD) в форме.

Если ваш запрос будет одобрен, вы получите электронное письмо с инструкциями, описывающие, как получить учетные данные и доступ к реестру закрытый контейнер.

## <a name="log-in-to-the-private-container-registry"></a>Вход в частный реестр контейнеров

Существует несколько способов проверки подлинности с помощью закрытого реестра контейнеров для контейнеров Cognitive Services. Мы рекомендуем использовать метод командной строки с помощью [Docker CLI](https://docs.docker.com/engine/reference/commandline/cli/).

Используйте [docker login](https://docs.docker.com/engine/reference/commandline/login/) команды, как показано в следующем примере, чтобы войти в `containerpreview.azurecr.io`, который является частный реестр контейнеров для контейнеров Cognitive Services. Замените *\<username\>* на имя пользователя, а *\<password\>* — на пароль, предоставленные в учетных данных, полученных от команды Azure Cognitive Services.

```
docker login containerpreview.azurecr.io -u <username> -p <password>
```

Если вы защитили свои учетные данные в текстовый файл, содержимое этого файла текста можно сцепить `docker login` команды. Используйте `cat` команды, как показано в следующем примере. Замените *\<passwordFile\>* путь и имя текстового файла, который содержит пароль. Замените *\<username\>* с именем пользователя, учетные данные.

```
cat <passwordFile> | docker login containerpreview.azurecr.io -u <username> --password-stdin
```

