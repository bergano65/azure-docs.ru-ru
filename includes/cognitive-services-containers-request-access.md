---
author: IEvangelist
ms.author: dapine
ms.service: cognitive-services
ms.topic: include
ms.date: 03/04/2020
ms.openlocfilehash: 88d83676de1e7fa18c4c1dcbf347da8d685ba2fa
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/19/2020
ms.locfileid: "83593343"
---
Заполните и отправьте [форму запроса Cognitive Services контейнеров](https://aka.ms/cognitivegate) , чтобы запросить доступ к контейнеру. В форме нужно указать сведения о себе, компании и пользовательском сценарии, для которого будет использоваться контейнер. После отправки формы команда Azure Cognitive Services проверит ее, чтобы убедиться, что вы отвечаете критериям доступа к частному реестру контейнеров.

> [!IMPORTANT]
> Необходимо использовать адрес электронной почты, связанный с учетной записью Майкрософт (MSA) или учетной записью Azure Active Directory (Azure AD) в форме. Дополнительные сведения о критериях приемки см. в разделе [процесс Cognitive Servicesности](../articles/cognitive-services/cognitive-services-gating-process.md).

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

