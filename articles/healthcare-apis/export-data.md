---
title: Запуск экспорта путем вызова команды $export в API Azure для FHIR
description: В этой статье описывается, как настроить и использовать неидентифицированный экспорт.
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 8/26/2020
ms.author: matjazl
ms.openlocfilehash: ecc2134d1a528ee22710cb447f996e0c5e31a8de
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/25/2020
ms.locfileid: "91308186"
---
# <a name="how-to-export-fhir-data"></a>Экспорт данных FHIR

Перед использованием $export необходимо убедиться, что API Azure для FHIR настроен для использования. Сведения о настройке параметров экспорта и создании учетной записи хранения Azure см. на [странице Настройка экспорта данных](configure-export-data.md).

## <a name="using-export-command"></a>Использование команды $export

После настройки API Azure для FHIR для экспорта можно использовать команду $export, чтобы экспортировать данные из службы. Данные будут храниться в учетной записи хранения, указанной при настройке экспорта. Чтобы узнать, как вызвать команду $export на сервере FHIR, ознакомьтесь с документацией по [спецификации $Export](https://hl7.org/Fhir/uv/bulkdata/export/index.html). 

Команда $export в API Azure для FHIR принимает необязательный параметр _ \_ контейнера_ , который указывает контейнер в настроенной учетной записи хранения, куда должны быть экспортированы данные.

`https://<<FHIR service base URL>>/$export?_container=<<container_name>>`

## <a name="supported-scenarios"></a>Поддерживаемые сценарии

API Azure для FHIR поддерживает $export на уровне системы, пациента и группы. При экспорте группы экспортируются все связанные ресурсы, но не экспортируются характеристики группы.

> [!Note] 
> $export будет экспортировать дублирующиеся ресурсы, если ресурс находится в секции более чем одного ресурса или находится в нескольких группах.

## <a name="next-steps"></a>Дальнейшие действия

Из этой статьи вы узнали, как экспортировать ресурсы FHIR с помощью команды $export. Далее вы можете ознакомиться с поддерживаемыми функциями.
 
>[!div class="nextstepaction"]
>[Поддерживаемые функции](fhir-features-supported.md)
