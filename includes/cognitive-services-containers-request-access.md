---
author: diberry
ms.author: diberry
ms.service: cognitive-services
ms.topic: include
ms.date: 01/24/2019
ms.openlocfilehash: 4cdcec850f32d7e94f33eb28e5bf7839e511f347
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66124590"
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

