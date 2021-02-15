---
title: Customer-Managed ключи для Cognitive Services
titleSuffix: Cognitive Services
description: Узнайте, как использовать портал Azure для настройки ключей, управляемых клиентом, с помощью Azure Key Vault. Управляемые клиентом ключи позволяют создавать, менять, отключать и отзывать элементы управления доступом.
services: cognitive-services
author: erindormier
ms.service: cognitive-services
ms.topic: include
ms.date: 05/28/2020
ms.author: egeaney
ms.openlocfilehash: fca3fc80411432f2e82a68faa3b6f297b8ca5f9c
ms.sourcegitcommit: 27d616319a4f57eb8188d1b9d9d793a14baadbc3
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/15/2021
ms.locfileid: "100522008"
---
# <a name="configure-customer-managed-keys-with-azure-key-vault-for-cognitive-services"></a>Настройка ключей, управляемых клиентом, с помощью Azure Key Vault для Cognitive Services

Процесс включения Customer-Managed ключей с Azure Key Vault для Cognitive Services зависит от продукта. Используйте эти ссылки для получения инструкций, относящихся к службам.

## <a name="vision"></a>Компьютерное зрение

* [Пользовательское визуальное распознавание шифрование неактивных данных](../Custom-Vision-Service/encrypt-data-at-rest.md)
* [Шифрование неактивных данных в службах распознавания лиц](../Face/encrypt-data-at-rest.md)
* [Расшифровка неактивных данных распознавателем форм](../form-recognizer/encrypt-data-at-rest.md)

## <a name="language"></a>Язык

* [Распознавание речи шифрование неактивных данных в службе](../LUIS/encrypt-data-at-rest.md)
* [QnA Maker шифрование неактивных данных](../QnAMaker/encrypt-data-at-rest.md)
* [Шифрование неактивных данных в преобразователе](../translator/encrypt-data-at-rest.md)

## <a name="decision"></a>Решение

* [Content Moderator шифрование неактивных данных](../Content-Moderator/encrypt-data-at-rest.md)
* [Шифрование неактивных данных персонализации](../personalizer/encrypt-data-at-rest.md)

## <a name="next-steps"></a>Дальнейшие действия

* [Об Azure Key Vault](../../key-vault/general/overview.md)
* [Форма запроса ключа Cognitive Services Customer-Managed](https://aka.ms/cogsvc-cmk)