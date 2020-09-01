---
title: Запуск экспорта путем вызова команды $export в API Azure для FHIR
description: В этой статье описывается, как настроить и использовать неидентифицированный экспорт.
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 8/26/2020
ms.author: matjazl
ms.openlocfilehash: 10d901f73006051e8b1ddd02aeb36b229c6a7761
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/01/2020
ms.locfileid: "89270285"
---
# <a name="how-to-export-fhir-data"></a>Экспорт данных FHIR

Сведения о настройке параметров экспорта и создании учетной записи хранения Azure см. [здесь](configure-export-data.md).

## <a name="exporting-fhir-resources-using-export-command"></a>Экспорт ресурсов FHIR с помощью команды $export

После настройки API Azure для FHIR теперь можно использовать команду $export, чтобы экспортировать данные из службы в учетную запись хранения, указанную при настройке экспорта. Чтобы узнать, как вызвать команду $export на сервере FHIR, ознакомьтесь с документацией по $export спецификации по адресу [https://hl7.org/Fhir/uv/bulkdata/export/index.html](https://hl7.org/Fhir/uv/bulkdata/export/index.html) . 

Команда $export в API Azure для FHIR принимает необязательный параметр _ \_ конатинер_ , который можно использовать для указания контейнера в настроенной учетной записи хранения, в которую должны быть экспортированы данные.

`https://<<FHIR service base URL>>/$export?_container=<<container_name>>`

> [!IMPORTANT]
> Обратите внимание, что в настоящее время API Azure для FHIR поддерживает только экспорт на уровне системы, как определено в спецификации $export по адресу [https://hl7.org/Fhir/uv/bulkdata/export/index.html](https://hl7.org/Fhir/uv/bulkdata/export/index.html) . Кроме того, только _ \_ так как_ параметр запроса в настоящее время поддерживается.

## <a name="exporting-de-identified-data-preview"></a>Экспорт неопознанных данных (Предварительная версия)

Команду $export также можно использовать для экспорта неопознанных данных с сервера FHIR. Он использует механизм анонимности из [FHIR Tools для анонимного](https://github.com/microsoft/FHIR-Tools-for-Anonymization)выполнения и принимает сведения о конфигурации анонимного использования в параметрах запроса. Вы можете создать собственный файл конфигурации анонимного пользователя или использовать [Пример файла конфигурации](https://github.com/microsoft/FHIR-Tools-for-Anonymization#sample-configuration-file-for-hipaa-safe-harbor-method) для Harbor в качестве отправной точки. 

 `https://<<FHIR service base URL>>/$export?_container=<<container_name>>&_anonymizationConfig=<<config file name>>&_anonymizationConfigEtag=<<ETag on storage>>`

|Параметр запроса            | Пример |Optionality| Описание|
|---------------------------|---------|-----------|------------|
| _\_анонимизатионконфиг_   |DemoConfig.jsна|Требуется для отмены идентификации экспорта |Имя файла конфигурации. См [. формат](https://github.com/microsoft/FHIR-Tools-for-Anonymization#configuration-file-format)файла конфигурации. Этот файл должен храниться внутри контейнера с именем " **анонимность** " в той же учетной записи хранения Azure, которая настроена в качестве расположения экспорта. |
| _\_анонимизатионконфижетаг_|"0x8D8494A069489EC"|Необязательно для отмены определения экспорта|Это ETag файла конфигурации. Вы можете получить ETag с помощью обозревателя службы хранилища Azure из свойства BLOB-объекта.|

> [!IMPORTANT]
> Обратите внимание, что как необработанный экспорт, так и неопознанный экспорт записываются в ту же учетную запись хранения Azure, которая указана в конфигурации экспорта. Рекомендуется использовать разные контейнеры, соответствующие разным неопознанным конфигурациям, и управлять доступом пользователей на уровне контейнера.
