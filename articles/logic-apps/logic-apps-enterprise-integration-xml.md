---
title: XML-сообщения и неструктурированные файлы — Azure Logic Apps
description: Обработка, проверка и преобразование XML-сообщений в Azure Logic Apps с помощью Пакет интеграции Enterprise
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.date: 02/27/2017
ms.openlocfilehash: 730adf3e6ef3ddab5a8b0e927f94ffe3725358f4
ms.sourcegitcommit: d37991ce965b3ee3c4c7f685871f8bae5b56adfa
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/21/2019
ms.locfileid: "72679910"
---
# <a name="xml-messages-and-flat-files-in-azure-logic-apps-with-enterprise-integration-pack"></a>XML-сообщения и неструктурированные файлы в Azure Logic Apps с Пакетом интеграции Enterprise

В [Azure Logic Apps](logic-apps-overview.md)можно ОБРАБАТЫВАТЬ сообщения XML, которые вы отправляете и получаете, используя пакет интеграции Enterprise. Если вы использовали BizTalk Server, Пакет интеграции Enterprise предоставляет аналогичные возможности для преобразования и проверки сообщений, работы с неструктурированными файлами и даже использования XPath для обогащения или извлечения конкретных свойств сообщения. Если вы не знакомы с этим пространством, эти функции расширяют способ обработки сообщений в рабочем процессе приложения логики. Например, при наличии сценария "бизнес-бизнес" (B2B) и работы с конкретными XML-схемами можно использовать Пакет интеграции Enterprise, чтобы усовершенствовать процесс обработки этих сообщений в вашей компании.

Например, Пакет интеграции Enterprise включает следующие возможности:

* [Проверка XML](logic-apps-enterprise-integration-xml-validation.md): Проверка входящего или исходящего сообщения XML для определенной схемы.

* [Преобразование XML](logic-apps-enterprise-integration-transform.md): преобразование или Настройка сообщения XML в зависимости от требований или требований партнера с помощью карт.

* Кодирование неструктурированных [файлов и декодирование неструктурированных файлов](logic-apps-enterprise-integration-flatfile.md): кодирование или декодирование неструктурированного файла.

  Например, SAP принимает и отправляет IDOC-файлы в виде неструктурированных файлов. Многие платформы интеграции создают сообщения XML, включая Logic Apps. Поэтому можно создать приложение логики, которое использует кодировщик неструктурированных файлов для преобразования XML-файлов в неструктурированные файлы.

* [XPath](workflow-definition-language-functions-reference.md#xpath): дополняет сообщение и извлекает определенные свойства из сообщения. Эти извлеченные свойства можно затем использовать для перенаправления сообщения в целевую или промежуточную конечную точку.

## <a name="sample"></a>Пример

[Разверните полностью работоспособное приложение логики](https://github.com/Azure/azure-quickstart-templates/tree/master/201-logic-app-veter-pipeline) (пример GitHub) с помощью функций XML в Azure Logic Apps.

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о [пакет интеграции Enterprise](logic-apps-enterprise-integration-overview.md)
