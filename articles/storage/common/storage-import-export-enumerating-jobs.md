---
title: Вывод списка всех заданий импорта и экспорта Azure | Документация Майкрософт
description: Узнайте, как вывести список всех заданий службы импорта и экспорта Azure в подписке.
author: muralikk
services: storage
ms.service: storage
ms.topic: article
ms.date: 01/23/2017
ms.author: muralikk
ms.subservice: common
ms.openlocfilehash: 0ae9e7fa76c8ecbb724cf0f494e648df989dff30
ms.sourcegitcommit: 3aa0fbfdde618656d66edf7e469e543c2aa29a57
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/05/2019
ms.locfileid: "55726673"
---
# <a name="enumerating-jobs-in-the-azure-importexport-service"></a>Перечисление заданий в службе импорта и экспорта Azure
Чтобы перечислить все задания в подписке, вызовите операцию [List Jobs](/rest/api/storageimportexport/jobs). `List Jobs` возвращает список заданий, а также перечисленные ниже атрибуты:

-   тип задания (импорт или экспорт);

-   текущее состояние задания;

-   связанная с задание учетная запись хранения.

## <a name="next-steps"></a>Дополнительная информация

* [Использование REST API службы импорта и экспорта Azure](storage-import-export-using-the-rest-api.md)
