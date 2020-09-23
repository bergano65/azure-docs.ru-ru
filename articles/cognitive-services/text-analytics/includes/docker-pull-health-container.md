---
title: Извлечение DOCKER для контейнера работоспособности
titleSuffix: Azure Cognitive Services
description: Команда DOCKER Pull для Анализ текста контейнере работоспособности
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 07/07/2020
ms.author: aahi
ms.openlocfilehash: b9452baf0301233a1cd6e05621a89d013d32c9e0
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/22/2020
ms.locfileid: "90906011"
---
Заполните и отправьте [форму запроса Cognitive Services контейнеров](https://aka.ms/cognitivegate) , чтобы запросить доступ к контейнеру.
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
