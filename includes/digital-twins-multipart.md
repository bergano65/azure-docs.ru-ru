---
title: включить файл
description: включить файл
services: digital-twins
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
ms.topic: include
ms.date: 12/12/2019
ms.custom: include file
ms.openlocfilehash: 6fa21d78d766ee8e541d6d60f64628b55a84fe41
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75468135"
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

Составные запросы выполняются программно (с использованием C#), с помощью клиента REST или средства, например [Postman](https://docs.microsoft.com/azure/digital-twins/how-to-configure-postman#make-a-multipart-post-request). Клиентские средства REST могут предоставлять различные уровни поддержки для сложных составных запросов. Параметры конфигурации также могут немного отличаться в зависимости от средства. Проверьте, какое средство лучше всего подходит для вас.

> [!IMPORTANT]
> Составные запросы к API управления Azure Digital Twins, как правило, состоят из двух частей:
> * метаданные больших двоичных объектов (такие как связанный тип MIME), которые объявлены с помощью **Content-Type** и/или **Content-Disposition**;
> * содержимое большого двоичного объекта, в том числе неструктурированное содержимое файла для отправки.
>
> Ни одна из двух частей не требуется для запросов **PATCH**. Они необходимы для **POST** или операций создания.

[Исходный код для быстрого определения степени заполненности](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/occupancy-quickstart/src/api/update.cs) содержит полные примеры C# для отправки составных запросов к API управления Azure Digital Twins.