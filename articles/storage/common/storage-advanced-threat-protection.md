---
title: Настройка расширенной защиты от угроз
titleSuffix: Azure Storage
description: Настройте расширенную защиту от угроз для службы хранилища Azure, чтобы определить аномалии в действиях учетной записи и получить уведомления о потенциально опасных попытках доступа к вашей учетной записи.
services: storage
author: tamram
ms.service: storage
ms.subservice: common
ms.topic: conceptual
ms.date: 04/16/2020
ms.author: tamram
ms.reviewer: cbrooks
ms.openlocfilehash: 724c250b56107cb68da387bdd531602e8d239e1c
ms.sourcegitcommit: edccc241bc40b8b08f009baf29a5580bf53e220c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/24/2020
ms.locfileid: "82127531"
---
# <a name="configure-advanced-threat-protection-for-azure-storage"></a>Настройка расширенной защиты от угроз для службы хранилища Azure

Расширенная защита от угроз для службы хранилища Azure предоставляет дополнительный уровень логики безопасности, который обнаруживает необычные и потенциально опасные попытки доступа или использования учетных записей хранения. Этот уровень защиты позволяет устранять угрозы без эксперта по безопасности или управления системами мониторинга безопасности.

Оповещения системы безопасности активируются при возникновении аномалий в действии. Эти оповещения безопасности интегрируются с [центром безопасности Azure](https://azure.microsoft.com/services/security-center/), а также отправляются по электронной почте администраторам подписки с подробными сведениями о подозрительных действиях и рекомендациями по исследованию и устранению угроз.

Служба принимает журналы ресурсов запросов на чтение, запись и удаление в хранилище BLOB-объектов для обнаружения угроз. Чтобы исследовать предупреждения из Advanced Threat Protection, можно просмотреть связанные действия с хранилищем с помощью Аналитика Службы хранилища ведения журнала. Дополнительные сведения см. в статье **Настройка ведения журналов** в [мониторинге учетной записи хранения в портал Azure](storage-monitor-storage-account.md#configure-logging).

## <a name="availability"></a>Доступность

В настоящее время Расширенная защита от угроз для службы хранилища Azure доступна только для [хранилища BLOB-объектов](https://azure.microsoft.com/services/storage/blobs/). Типы учетных записей, поддерживающие расширенную защиту от угроз, включают учетные записи общего назначения v2, блочного BLOB-объекта и хранилища BLOB-объектов. Расширенная защита от угроз доступна во всех общедоступных облаках и облаках правительства США, но не в других независимых или облачных регионах Azure для государственных организаций.

Сведения о ценах, включая бесплатную пробную версию на 30 дней, см. на [странице цен на центр безопасности Azure](https://azure.microsoft.com/pricing/details/security-center/).


## <a name="set-up-advanced-threat-protection"></a>Настройка расширенной защиты от угроз

Расширенную защиту от угроз можно настроить любым из нескольких способов, описанных в следующих разделах.

### <a name="portal"></a>[Портал](#tab/azure-portal)

1. Запустите [портал Azure](https://portal.azure.com/).
1. Перейдите к своей учетной записи хранения Azure. В разделе **Параметры**выберите пункт **Расширенная безопасность**.
1. На странице Настройка расширенной безопасности щелкните ссылку **Параметры** .
1. Установите для параметра **повышенная безопасность** значение **вкл**.
1. Нажмите кнопку **сохранить** , чтобы сохранить новую или обновленную политику.

    ![Включение Расширенной защиты от угроз для службы хранилища Azure](./media/storage-advanced-threat-protection/storage-advanced-threat-protection-turn-on.png)

### <a name="azure-security-center"></a>[Центр безопасности Azure](#tab/azure-security-center)

При оформлении подписки на уровень "Стандартный" в центре безопасности Azure автоматически настраивается для всех учетных записей хранения. Вы можете включить или отключить расширенную защиту от угроз для учетных записей хранения в определенной подписке следующим образом:

1. Запустите **Центр безопасности Azure** на [портал Azure](https://portal.azure.com).
1. В главном меню щелкните **цены & параметры**.
1. Щелкните подписку, для которой необходимо включить или отключить защиту от угроз для учетных записей хранения.

    ![Выбор подписки](./media/storage-advanced-threat-protection/storage-advanced-threat-protection-subscription.png)

1. Щелкните **ценовая**Категория.
1. В разделе **Выбор ценовой категории по типу ресурса** в строке **учетные записи хранения** щелкните **включено** или **отключено**.

    ![Включение ATP в центре безопасности](./media/storage-advanced-threat-protection/storage-advanced-threat-protection-pricing2.png)
1. Нажмите кнопку **Сохранить**.

### <a name="template"></a>[Шаблон](#tab/template)

Используйте шаблон Azure Resource Manager, чтобы развернуть учетную запись хранения Azure с включенной службой Advanced Threat protection. Дополнительные сведения см. [в разделе Учетная запись хранения с расширенной защитой угроз](https://azure.microsoft.com/resources/templates/201-storage-advanced-threat-protection-create/).

### <a name="azure-policy"></a>[Политика Azure](#tab/azure-policy)

Используйте политику Azure, чтобы включить расширенную защиту от угроз для учетных записей хранения в определенной подписке или группе ресурсов.

1. Откройте страницу **определения политики** Azure.

1. Выполните поиск по запросу политика **развертывания Advanced Threat Protection в учетных записях хранения** .

     ![Поиск политики](./media/storage-advanced-threat-protection/storage-atp-policy-definitions.png)

1. Выберите подписку Azure или группу ресурсов.

    ![Выберите подписку или группу](./media/storage-advanced-threat-protection/storage-atp-policy2.png)

1. Назначьте политику.

    ![Страница "определения политик"](./media/storage-advanced-threat-protection/storage-atp-policy1.png)

### <a name="rest-api"></a>[REST API](#tab/rest-api)

Используйте команды API-интерфейса для создания, обновления или получения параметра Advanced Threat Protection для конкретной учетной записи хранения.

* [Расширенная защита от угроз — создание](https://docs.microsoft.com/rest/api/securitycenter/advancedthreatprotection/create)
* [Расширенная защита от угроз — получение](https://docs.microsoft.com/rest/api/securitycenter/advancedthreatprotection/get)

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Используйте следующие командлеты PowerShell:

* [Включить расширенную защиту от угроз](https://docs.microsoft.com/powershell/module/az.security/enable-azsecurityadvancedthreatprotection)
* [Получить расширенную защиту от угроз](https://docs.microsoft.com/powershell/module/az.security/get-azsecurityadvancedthreatprotection)
* [Отключить Advanced Threat protection](https://docs.microsoft.com/powershell/module/az.security/disable-azsecurityadvancedthreatprotection)

---

## <a name="explore-security-anomalies"></a>Изучение аномалий безопасности

При возникновении аномальных операций с хранилищем вы получите уведомление по электронной почте с информацией о подозрительном событии безопасности. Вы получите следующие сведения о событии:

* Характер аномалии
* имя учетной записи хранения.
* Время события
* Тип хранилища
* Возможные причины
* Действия по исследованию
* Действия по исправлению

Кроме того, в электронном сообщении приводится информация о возможных причинах, а также рекомендуемые действия по поиску и устранению потенциальной угрозы.

![Расширенная защита от угроз для службы хранилища Azure: оповещение по электронной почте](./media/storage-advanced-threat-protection/storage-advanced-threat-protection-alert-email.png)

Вы можете просматривать текущие оповещения системы безопасности и управлять ими на [плитке оповещения системы](../../security-center/security-center-managing-and-responding-alerts.md)безопасности центра безопасности Azure. Если щелкнуть какое-либо оповещение, отобразятся сведения и действия, позволяющие изучить текущую угрозу и устранить будущие угрозы.

![Расширенная защита от угроз для службы хранилища Azure: оповещение по электронной почте](./media/storage-advanced-threat-protection/storage-advanced-threat-protection-alert.png)

## <a name="security-alerts"></a>Оповещения безопасности

Оповещения создаются при нестандартных и потенциально вредоносных попытках получить доступ к учетным записям хранения или использовать их. Список оповещений для службы хранилища Azure см. в разделе " **хранилище** " статьи [Защита от угроз для служб данных в центре безопасности Azure](https://docs.microsoft.com/azure/security-center/alerts-reference#alerts-azurestorage).

## <a name="next-steps"></a>Следующие шаги

* Дополнительные сведения о [журналах в учетных записях хранения Azure](/rest/api/storageservices/About-Storage-Analytics-Logging)
* Дополнительные сведения о [центре безопасности Azure](../../security-center/security-center-intro.md)
