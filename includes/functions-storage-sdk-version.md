---
title: Включить имя файла
description: включить файл
services: functions
author: craigshoemaker
manager: gwallace
ms.service: azure-functions
ms.topic: include
ms.date: 01/28/2020
ms.author: cshoe
ms.custom: include file
ms.openlocfilehash: 6a37850eb6536c5399d63144e60ea210fbc194d8
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "77198430"
---
#### <a name="azure-storage-sdk-version-in-functions-1x"></a>Версия пакета SDK для службы хранилища Azure в решении "Функции" 1.x

В решении "Функции" 1.x триггеры и привязки службы хранилища используют версию 7.2.1 пакета SDK для службы хранилища Azure (пакет NuGet [WindowsAzure.Storage](https://www.nuget.org/packages/WindowsAzure.Storage/7.2.1)). Если указать другую версию пакета SDK для службы хранилища и использовать привязку к типу пакета SDK для службы хранилища в сигнатуре функции, то среда выполнения "Функции" может сообщить, что выполнить привязку к этому типу невозможно. Чтобы избежать этого, убедитесь, в вашем проекте указан пакет [WindowsAzure.Storage 7.2.1](https://www.nuget.org/packages/WindowsAzure.Storage/7.2.1).
