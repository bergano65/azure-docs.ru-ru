---
title: Извлечение DOCKER для контейнера работоспособности
titleSuffix: Azure Cognitive Services
description: Команда DOCKER Pull для Анализ текста контейнере работоспособности
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 07/07/2020
ms.author: aahi
ms.openlocfilehash: 519625f6468372ec7ace523dae7648212f4f3203
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "91779558"
---
Заполните и отправьте [форму запроса Cognitive Services контейнеров](https://aka.ms/csgate) , чтобы запросить доступ к контейнеру.
В форме нужно указать сведения о себе, компании и пользовательском сценарии, для которого будет использоваться контейнер. После отправки формы команда Azure Cognitive Services просматривает ее и проверяет, соответствуете ли вы критериям доступа к частному реестру контейнеров.

> [!IMPORTANT]
> * В форме необходимо использовать адрес электронной почты, связанный с ИДЕНТИФИКАТОРом подписки Azure.
> * Ресурс Azure, используемый для запуска контейнера, должен быть создан с утвержденным ИДЕНТИФИКАТОРом подписки Azure. 
> * Проверьте электронную почту (папки "Входящие" и "спам") на наличие обновлений для приложения от корпорации Майкрософт.

Используйте команду DOCKER login с учетными данными, предоставленными в электронной почте для подключения к закрытому реестру контейнеров для Cognitive Services контейнеров.


```Docker
docker login containerpreview.azurecr.io -u <username> -p <password>
```

Используйте [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) команду, чтобы скачать этот образ контейнера из закрытого реестра контейнеров.

```
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-healthcare:latest
```
