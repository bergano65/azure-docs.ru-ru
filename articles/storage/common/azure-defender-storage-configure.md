---
title: Настройка защитника Azure для хранилища
titleSuffix: Azure Storage
description: Настройте защитник Azure для хранения данных, чтобы выявить аномалии в действиях учетной записи и получать уведомления о потенциально опасных попытках доступа к вашей учетной записи.
services: storage
author: tamram
ms.service: storage
ms.subservice: common
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: tamram
ms.reviewer: ozgun
ms.openlocfilehash: d2ad302042b277cf29b3a7b22af88b662686b3fd
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/22/2020
ms.locfileid: "90995950"
---
# <a name="configure-azure-defender-for-storage"></a>Настройка защитника Azure для хранилища

Защитник Azure для хранилища предоставляет дополнительный уровень логики безопасности, который обнаруживает необычные и потенциально опасные попытки доступа или использования учетных записей хранения. Этот уровень защиты позволяет устранять угрозы без эксперта по безопасности или управления системами мониторинга безопасности.

Оповещения системы безопасности активируются при возникновении аномальных действий. Эти оповещения безопасности интегрируются с [центром безопасности Azure](https://azure.microsoft.com/services/security-center/), а также отправляются по электронной почте администраторам подписки с подробными сведениями о подозрительных действиях и рекомендациями по исследованию и устранению угроз.

Служба принимает журналы ресурсов для запросов на чтение, запись и удаление в хранилище BLOB-объектов и в службе файлов Azure (Предварительная версия) для обнаружения угроз. Для анализа оповещений из защитника Azure можно просмотреть связанные действия с хранилищем с помощью Аналитика Службы хранилища ведения журнала. Дополнительные сведения см. в статье **Настройка ведения журналов** в [мониторинге учетной записи хранения в портал Azure](storage-monitor-storage-account.md#configure-logging).

## <a name="availability"></a>Доступность

Сейчас защитник Azure для хранилища доступен для хранилища BLOB-объектов, файлов Azure (Предварительная версия) и Azure Data Lake Storage 2-го поколения (Предварительная версия). Типы учетных записей, поддерживающие защитник Azure, включают учетные записи общего назначения версии 2, блочного BLOB-объекта и хранилища BLOB-объектов. Защитник Azure для хранилища доступен во всех общедоступных облаках и облаках правительства США, но не в других независимых или облачных регионах Azure для государственных организаций.

Учетные записи с иерархическими пространствами имен, включенными для Data Lake Storage, поддерживают транзакции с использованием API-интерфейсов хранилища BLOB-объектов Azure и Data Lake Storage API. Файловые ресурсы Azure поддерживают транзакции по протоколу SMB.

Сведения о ценах, включая бесплатную пробную версию на 30 дней, см. на [странице цен на центр безопасности Azure](https://azure.microsoft.com/pricing/details/security-center/).

В следующем списке перечислены сведения о доступности защитника Azure для хранилища.

- Состояние выпуска:
  - [Хранилище BLOB-объектов](https://azure.microsoft.com/services/storage/blobs/) (общая доступность)
  - Службы [файлов Azure](https://docs.microsoft.com/azure/storage/files/storage-files-introduction) (Предварительная версия поддерживает транзакции SMB и RESTful)
  - Azure Data Lake Storage 2-го поколения (предварительная версия)
- Облако.<br>
    ✔ коммерческие облака;<br>
    ✔ US Gov<br>
    ✘ Китая gov, другие gov

## <a name="set-up-azure-defender"></a>Настройка защитника Azure

Вы можете настроить защитник Azure для хранения несколькими способами, описанными в следующих разделах.

### <a name="azure-security-center"></a>[Центр безопасности Azure](#tab/azure-security-center)

При оформлении подписки на уровень "Стандартный" в центре безопасности Azure защитник Azure автоматически настраивается для всех учетных записей хранения. Вы можете включить или отключить защитник Azure для учетных записей хранения в определенной подписке следующим образом:

1. Запустите **Центр безопасности Azure** на [портал Azure](https://portal.azure.com).
1. В главном меню в разделе **Управление**выберите **параметры ценообразования &**.
1. Выберите подписку, для которой вы хотите включить или отключить защитник Azure.
1. Выберите **защитник Azure** , чтобы включить защитник Azure для подписки.
1. В разделе **Выбор плана защитника Azure по типу ресурса**выберите строку **хранилища** и выберите **включено** в столбце **план** .
1. Сохраните изменения.

    :::image type="content" source="media/azure-defender-storage-configure/enable-azure-defender-security-center.png" alt-text="Снимок экрана, показывающий, как включить защитник Azure для хранилища в центре безопасности":::

Теперь защитник Azure включен для всех учетных записей хранения в этой подписке.

### <a name="portal"></a>[Портал](#tab/azure-portal)

1. Запустите [портал Azure](https://portal.azure.com/).
1. Войдите в свою учетную запись хранения. В разделе **Параметры**выберите пункт **Расширенная безопасность**.
1. Выберите **включить защитник Azure для хранилища**.

    :::image type="content" source="media/azure-defender-storage-configure/enable-azure-defender-portal.png" alt-text="Снимок экрана, показывающий, как включить защитник Azure для учетной записи хранения Azure":::

Теперь для этой учетной записи хранения включена служба "защитник Azure".

### <a name="template"></a>[Шаблон](#tab/template)

Используйте шаблон Azure Resource Manager для развертывания учетной записи хранения Azure с включенным защитником Azure. Дополнительные сведения см. [в разделе Учетная запись хранения с расширенной защитой угроз](https://azure.microsoft.com/resources/templates/201-storage-advanced-threat-protection-create/).

### <a name="azure-policy"></a>[Политика Azure](#tab/azure-policy)

Используйте политику Azure, чтобы включить защитник Azure для учетных записей хранения в определенной подписке или группе ресурсов.

1. Откройте страницу **определения политики** Azure.
1. Найдите политику **развертывание защитника Azure в учетных записях хранения** .

    :::image type="content" source="media/azure-defender-storage-configure/storage-atp-policy-definitions.png" alt-text="Применение политики для включения защитника Azure для учетных записей хранения":::

1. Выберите подписку Azure или группу ресурсов.

    :::image type="content" source="media/azure-defender-storage-configure/storage-atp-policy2.png" alt-text="Выберите подписку или группу ресурсов для области политики. ":::

1. Назначьте политику.

    :::image type="content" source="media/azure-defender-storage-configure/storage-atp-policy1.png" alt-text="Назначение политики для включения защитника Azure для хранилища":::

### <a name="rest-api"></a>[REST API](#tab/rest-api)

Используйте команды API-интерфейса для создания, обновления или получения параметра защитника Azure для конкретной учетной записи хранения.

- [Расширенная защита от угроз — создание](https://docs.microsoft.com/rest/api/securitycenter/advancedthreatprotection/create)
- [Расширенная защита от угроз — получение](https://docs.microsoft.com/rest/api/securitycenter/advancedthreatprotection/get)

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Используйте следующие командлеты PowerShell:

- [Включить расширенную защиту от угроз](https://docs.microsoft.com/powershell/module/az.security/enable-azsecurityadvancedthreatprotection)
- [Получить расширенную защиту от угроз](https://docs.microsoft.com/powershell/module/az.security/get-azsecurityadvancedthreatprotection)
- [Отключить Advanced Threat protection](https://docs.microsoft.com/powershell/module/az.security/disable-azsecurityadvancedthreatprotection)

---

## <a name="explore-security-anomalies"></a>Изучение аномалий безопасности

При возникновении аномальных операций с хранилищем вы получите уведомление по электронной почте с информацией о подозрительном событии безопасности. Вы получите следующие сведения о событии:

- Характер аномалии
- Имя учетной записи хранения.
- Время события
- Тип хранилища
- Возможные причины
- Действия по исследованию
- Действия по исправлению

Кроме того, в электронном сообщении приводится информация о возможных причинах, а также рекомендуемые действия по поиску и устранению потенциальной угрозы.

:::image type="content" source="media/azure-defender-storage-configure/storage-advanced-threat-protection-alert-email.png" alt-text="Оповещение службы "защитник Azure для хранилища"":::

Вы можете просматривать текущие оповещения системы безопасности и управлять ими на [плитке оповещения системы](../../security-center/security-center-managing-and-responding-alerts.md)безопасности центра безопасности Azure. Если щелкнуть какое-либо оповещение, отобразятся сведения и действия, позволяющие изучить текущую угрозу и устранить будущие угрозы.

:::image type="content" source="media/azure-defender-storage-configure/storage-advanced-threat-protection-alert.png" alt-text="Оповещение о службе "защитник Azure для хранилища"":::

## <a name="security-alerts"></a>Оповещения безопасности

Оповещения создаются при нестандартных и потенциально вредоносных попытках получить доступ к учетным записям хранения или использовать их. Список оповещений для службы хранилища Azure см. в статье [оповещения для службы хранилища Azure](../../security-center/alerts-reference.md#alerts-azurestorage).

## <a name="next-steps"></a>Дальнейшие действия

- Дополнительные сведения о [журналах в учетных записях хранения Azure](/rest/api/storageservices/About-Storage-Analytics-Logging)
- Узнайте больше о [центре безопасности Azure](../../security-center/security-center-intro.md).
