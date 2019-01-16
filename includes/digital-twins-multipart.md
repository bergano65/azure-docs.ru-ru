---
title: включение файла
description: включение файла
services: digital-twins
author: kingdomofends
ms.service: digital-twins
ms.topic: include
ms.date: 01/02/2019
ms.author: adgera
ms.custom: include file
ms.openlocfilehash: 6eb7993b4dbec3ab4901dc7071d18eae98ab8ae4
ms.sourcegitcommit: fbf0124ae39fa526fc7e7768952efe32093e3591
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/08/2019
ms.locfileid: "54079261"
---
> [!NOTE]
> Для составных запросов обычно требуются три компонента:
> * Заголовок **Content-Type**:
>   * `application/json; charset=utf-8`
>   * `multipart/form-data; boundary="USER_DEFINED_BOUNDARY"`
> * Объект **Content-Disposition**:
>   * `form-data; name="metadata"`
> * Содержимое файла для отправки.
>
> Применяемые **Content-Type** и **Content-Disposition** будут различаться в зависимости от сценария использования.

Составные запросы выполняются программно (с использованием C#), с помощью клиента REST или средства, например [Postman](https://www.getpostman.com/). Клиентские средства REST могут предоставлять различные уровни поддержки для сложных составных запросов. Проверьте, какое средство лучше всего подходит для вас.

> [!IMPORTANT]
> Составные запросы к API управления Azure Digital Twins состоят из двух частей:
> * метаданные больших двоичных объектов (такие как связанный тип MIME), которые объявлены с помощью **Content-Type** и **Content-Disposition**;
> * содержимое большого двоичного объекта, в том числе неструктурированное содержимое файла для отправки.
>
> Ни одна из двух частей не требуется для запросов **PATCH**. Они необходимы для **POST** или операций создания.

[Исходный код для быстрого определения степени заполненности](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/occupancy-quickstart/src/api/update.cs) содержит полные примеры C# для отправки составных запросов к API управления Azure Digital Twins.