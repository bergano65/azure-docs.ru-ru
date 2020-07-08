---
title: Версии Azure Key Vault
description: Различные версии Azure Key Vault
services: key-vault
author: msmbaldwin
ms.service: key-vault
ms.topic: conceptual
ms.date: 06/30/2019
ms.author: mbaldwin
ms.openlocfilehash: 4959d530e1f7a3c777aa3db21fe577c853fbca13
ms.sourcegitcommit: 9b5c20fb5e904684dc6dd9059d62429b52cb39bc
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "85855999"
---
# <a name="key-vault-versions"></a>Версии Key Vault

Вот именно новые Azure Key Vault. Новые функции и улучшения также объявляются в [канале обновлений Azure Key Vault](https://azure.microsoft.com/updates/?category=security&query=Key%20vault).

## <a name="june-2020"></a>июнь 2020 г.

Azure Monitor для Key Vault теперь находится на этапе предварительной версии.  Azure Monitor обеспечивает полный мониторинг хранилищ ключей, обеспечивая единое представление запросов Key Vault, производительности, сбоев и задержки. Дополнительные сведения см. в разделе [Azure Monitor for key Vault (Предварительная версия).](../../azure-monitor/insights/key-vault-insights-overview.md)

## <a name="may-2020"></a>Май 2020 г.

Key Vault "использовать собственный ключ" (BYOK) теперь общедоступен. Ознакомьтесь со [спецификацией Azure Key Vault BYOK](../keys/byok-specification.md)и Узнайте, как [импортировать ключи с защитой HSM в Key Vault (BYOK)](../keys/hsm-protected-keys-byok.md).

## <a name="march-2020"></a>Март 2020 г.

Частные конечные точки теперь доступны в предварительной версии. Служба "Частная связь Azure" позволяет получать доступ к Azure Key Vault и службам клиентов и партнеров Azure через закрытую конечную точку в виртуальной сети.  Узнайте, как [интегрировать Key Vault с помощью частной ссылки Azure](private-link-service.md).

## <a name="2019"></a>2019

- Выпуск пакетов SDK следующего поколения Azure Key Vault. Примеры использования см. в разделе краткие Azure Key Vault секретов для [Python](../secrets/quick-create-python.md), [.NET](../secrets/quick-create-net.md), [Java](../secrets/quick-create-java.md)и [Node.js](../secrets/quick-create-node.md)
- Новые политики Azure для управления сертификатами хранилища ключей. См. [встроенные определения политики Azure для Key Vault](../policy-samples.md).
- Azure Key Vault расширение виртуальной машины теперь общедоступно.  См. раздел [Key Vault расширение виртуальной машины для Linux](../../virtual-machines/extensions/key-vault-linux.md) и [Key Vault расширение виртуальной машины для Windows](../../virtual-machines/extensions/key-vault-windows.md).
- Управление секретами на основе событий для Azure Key Vault теперь доступно в службе "Сетка событий Azure". Дополнительные сведения см. в разделе [Схема сетки событий для событий в Azure Key Vault] (. /.. /Евент-ГРИД/Евент-счема-Кэй-Ваулт.МД] и Узнайте, как [получать уведомления хранилища ключей и отвечать на них с помощью службы "Сетка событий Azure](event-grid-tutorial.md)".

## <a name="2018"></a>2018

Новые функции и интеграции, выпущенные в этом году:

- Интеграция с функциями Azure. Пример сценария использования [функций Azure](../../azure-functions/index.yml) для операций с хранилищем ключей см. [в разделе Автоматизация смены секрета](../secrets/tutorial-rotation.md). 
- [Интеграция с Azure Databricks](/azure/databricks/scenarios/store-secrets-azure-key-vault). В результате Azure Databricks теперь поддерживает два типа секретных областей: Azure Key Vault и резервные. Дополнительные сведения см. [в разделе Создание области секрета с Azure Key Vault](/azure/databricks/security/secrets/secret-scopes#--create-an-azure-key-vault-backed-secret-scope) .
- [Конечные точки службы виртуальной сети для Azure Key Vault](overview-vnet-service-endpoints.md).

## <a name="2016"></a>2016

Новые функции, выпущенные в этом году:

- Ключи управляемой учетной записи хранения. Функция ключей учетной записи хранения добавила более простую интеграцию с хранилищем Azure. Дополнительные сведения см. в статье [Ключи учетной записи хранения Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-storage-keys).
- Обратимое удаление. Функция обратимого удаления улучшает защиту данных в хранилищах ключей и объектах хранилища ключей. Дополнительные сведения см. в статье [Общие сведения об обратимом удалении в Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete).

## <a name="2015"></a>2015

Новые функции, выпущенные в этом году:
- Управление сертификатами. Добавлена как функция в общедоступную версию 2015-06-01 от 26 сентября 2016 г.

Общая доступность (версия 2015-06-01) была объявлена 24 июня 2015 г. В этот выпуск внесены следующие изменения. 
- Удаление ключа. Удалено поле use.
- Получение сведений о ключе. Удалено поле use.
- Импорт ключа в хранилище. Удалено поле use.
- Восстановление ключа. Удалено поле use.     
- Был изменен алгоритм RSA (RSA_OAEP). См. [раздел о ключах, секретах и сертификатах](about-keys-secrets-certificates.md).    
 
Вторая Предварительная версия (версия 2015-02-01-Preview) была объявлена 20 апреля 2015. Дополнительные сведения см. в записи блога [REST API Update](https://blogs.technet.com/b/kv/archive/2015/04/20/empty-3.aspx) (Обновление REST API). Были обновлены следующие задачи:
 
- Составление списка ключей в хранилище. Добавлена поддержка разбиения на страницы для выполнения операции.
- Составление списка версий ключа. Добавлена операция перечисления версий ключа.  
- Составление списка секретов в хранилище. Добавлена поддержка разбиения на страницы.
- Составление списка версий секрета. Добавлена операция перечисления версий секрета.  
- Все операции. В атрибуты добавлены метки времени создания или обновления.  
- Создание секрета. В секреты добавлен Content-Type.
- Создание ключа. Добавлены теги в качестве дополнительных сведений.
- Создание секрета. Добавлены теги в качестве дополнительных сведений.
- Обновление ключа. Добавлены теги в качестве дополнительных сведений.
- Обновление секрета. Добавлены теги в качестве дополнительных сведений.
- Изменен максимальный размер для секретов (с 10 КБ на 25 КБ). Ознакомьтесь со статьей [About keys, secrets, and certificates](about-keys-secrets-certificates.md) (Сведения о ключах, секретах и сертификатах).    
 
## <a name="2014"></a>2014
 
Первая предварительная версия (версия 2014-12-08-Preview) была объявлена 8 января 2015.  
 
## <a name="next-steps"></a>Дальнейшие шаги

- [Сведения о ключах, секретах и сертификатах](about-keys-secrets-certificates.md)
- [Ключи](../keys/index.yml)
- [Секреты](../secrets/index.yml)
- [Сертификаты](../certificates/index.yml)
