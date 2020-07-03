---
author: aahill
ms.author: aahi
ms.service: cognitive-services
ms.topic: include
ms.date: 05/18/2020
ms.openlocfilehash: 66bd78c94e6c54d26959778cc059730c13d02629
ms.sourcegitcommit: 595cde417684e3672e36f09fd4691fb6aa739733
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2020
ms.locfileid: "83698563"
---
В форме нужно указать сведения о себе, компании и пользовательском сценарии, для которого будет использоваться контейнер. После отправки формы команда Azure Cognitive Services проверит ее, чтобы убедиться, что вы отвечаете критериям доступа к частному реестру контейнеров.

> [!IMPORTANT]
> Необходимо использовать адрес электронной почты, связанный с учетной записью Майкрософт (MSA) или учетной записью Azure Active Directory (Azure AD) в форме.

Если запрос утвержден, вы получите сообщение электронной почты с инструкциями, которые описывают, как получить учетные данные и получить доступ к частному реестру контейнеров.

## <a name="log-in-to-the-private-container-registry"></a>Вход в частный реестр контейнеров

Существует несколько способов проверки подлинности в закрытом реестре контейнеров для Cognitive Services контейнерах. Рекомендуется использовать метод командной строки с помощью [DOCKER CLI](https://docs.docker.com/engine/reference/commandline/cli/).

Используйте команду [DOCKER login](https://docs.docker.com/engine/reference/commandline/login/) , как показано в следующем примере, чтобы войти в `containerpreview.azurecr.io` , который является частным реестром контейнеров для Cognitive Services контейнеров. Замените * \< username \> * именем пользователя и * \< паролем \> * с паролем, указанным в учетных данных, полученных от команды Azure Cognitive Services.

```
docker login containerpreview.azurecr.io -u <username> -p <password>
```

Если учетные данные защищены в текстовом файле, можно объединить содержимое этого текстового файла с `docker login` командой. Используйте `cat` команду, как показано в следующем примере. Замените * \< passwordFile \> * на путь и имя текстового файла, содержащего пароль. Замените * \< username \> * именем пользователя, указанным в учетных данных.

```
cat <passwordFile> | docker login containerpreview.azurecr.io -u <username> --password-stdin
```

