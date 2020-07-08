---
title: Настройка параметров экспорта в API Azure для FHIR
description: В этой статье описывается, как настроить параметры экспорта в API Azure для FHIR.
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 3/5/2020
ms.author: matjazl
ms.openlocfilehash: 46a55b83b38593a514d40a9f75d99739a1efb8a2
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "84871024"
---
# <a name="configure-export-setting-and-export-the-data-to-a-storage-account"></a>Настройка параметров экспорта и экспорт данных в учетную запись хранения

API Azure для FHIR поддерживает $exportную команду, которая позволяет экспортировать данные из Azure API для учетной записи FHIR в учетную запись хранения.

Для выполнения экспорта в API Azure для FHIR выполните четыре шага:

1. Включение управляемого удостоверения в Azure API для службы FHIR
2. Создание учетной записи хранения Azure (если это еще не сделано) и назначение разрешения API Azure для FHIR учетной записи хранения
3. Выбор учетной записи хранения в API Azure для FHIR в качестве учетной записи экспорта хранения
4. Запуск экспорта путем вызова команды $export в API Azure для FHIR

## <a name="enabling-managed-identity-on-azure-api-for-fhir"></a>Включение управляемого удостоверения в API Azure для FHIR

Первый шаг в настройке API Azure для FHIR для экспорта заключается в включении управляемого на уровне системы удостоверения в службе. Сведения об управляемых удостоверениях в Azure можно прочитать [здесь](../active-directory/managed-identities-azure-resources/overview.md).

Для этого перейдите в Azure API for FHIR Service и выберите колонку удостоверений. Изменение состояния на вкл. включит управляемое удостоверение в Azure API для службы FHIR.

![Включить управляемое удостоверение](media/export-data/fhir-mi-enabled.png)

Теперь можно перейти к следующему шагу и создать учетную запись хранения и назначить ей разрешение.

## <a name="adding-permission-to-storage-account"></a>Добавление разрешения в учетную запись хранения

Следующим шагом экспорта является назначение разрешения на запись в учетную запись хранения в службе API Azure для FHIR.

После создания учетной записи хранения перейдите в колонку управление доступом (IAM) в учетной записи хранения и выберите добавить назначения ролей.

![Включить управляемое удостоверение](media/export-data/fhir-export-role-assignment.png)

Затем мы добавим участника данных роли большого двоичного объекта хранилища ролей в имя службы.

![Включить управляемое удостоверение](media/export-data/fhir-export-role-add.png)

Теперь мы готовы к следующему шагу, где мы можем выбрать учетную запись хранения в Azure API для FHIR в качестве учетной записи хранения по умолчанию для $export.

## <a name="selecting-the-storage-account-for-export"></a>Выбор учетной записи хранения для $export

Последним шагом перед вызовом команды $export является назначение учетной записи хранения Azure, которую Azure API для FHIR будет использовать для экспорта данных. Для этого перейдите в колонку интеграция в API Azure для службы FHIR в портал Azure и выберите учетную запись хранения. 

![Включить управляемое удостоверение](media/export-data/fhir-export-storage.png)

После этого все готово для экспорта данных с помощью команды $export.

## <a name="exporting-the-data-using-export-command"></a>Экспорт данных с помощью команды $export

После настройки API Azure для FHIR теперь можно использовать команду $export, чтобы экспортировать данные из службы в указанную учетную запись хранения. Чтобы узнать, как вызвать команду $export на сервере FHIR, ознакомьтесь с документацией по спецификации $export по адресу[https://hl7.org/Fhir/uv/bulkdata/export/index.html](https://hl7.org/Fhir/uv/bulkdata/export/index.html)

> [!IMPORTANT]
> Обратите внимание, что в настоящее время API Azure для FHIR поддерживает только экспорт на уровне системы, как определено в спецификации экспорта [https://hl7.org/Fhir/uv/bulkdata/export/index.html](https://hl7.org/Fhir/uv/bulkdata/export/index.html) . Сейчас мы также не поддерживаем параметры запроса с $export.

>[!div class="nextstepaction"]
>[Дополнительные параметры](azure-api-for-fhir-additional-settings.md)