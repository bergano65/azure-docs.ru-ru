---
title: Настройка расширенной защиты от угроз
titleSuffix: Azure Storage
description: Налажить расширенную защиту угроз для хранения azure для обнаружения аномалий в деятельности учетной записи и получать уведомления о потенциально опасных попытках доступа к вашей учетной записи.
services: storage
author: tamram
ms.service: storage
ms.subservice: common
ms.topic: conceptual
ms.date: 04/16/2020
ms.author: tamram
ms.reviewer: cbrooks
ms.openlocfilehash: f390aececdbf9a20f191279892c1856332f2aa8e
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81482094"
---
# <a name="configure-advanced-threat-protection-for-azure-storage"></a>Настройка расширенной защиты от угроз для хранения azure

Расширенная защита от угроз для Azure Storage предоставляет дополнительный уровень безопасности, который обнаруживает необычные и потенциально опасные попытки получить доступ к учетным записям или использовать их. Этот уровень защиты позволяет устракиваться в угрозах, не будучи экспертом по безопасности и не управляя системами мониторинга безопасности.

Оповещения безопасности срабатывают при возникновении аномалий в активности. Эти оповещения о безопасности интегрированы с [Azure Security Center,](https://azure.microsoft.com/services/security-center/)а также отправляются по электронной почте администраторам подписки с подробной информацией о подозрительной деятельности и рекомендациями о том, как расследовать и исправлять угрозы.

Служба запрашивает диагностические журналы чтения, записи и удаления запросов в Blob Storage для обнаружения угроз. Для изучения оповещений из расширенной защиты от угроз можно просмотреть связанные действия по хранению с помощью журналов analytics Storage. Для получения дополнительной информации на портале **«Настройка» —см.** [Monitor a storage account in the Azure portal](storage-monitor-storage-account.md#configure-logging)

## <a name="availability"></a>Доступность

Расширенная защита от угроз для хранения Azure в настоящее время доступна только для [хранилища Blob.](https://azure.microsoft.com/services/storage/blobs/) Типы учетных записей, поддерживающих расширенную защиту от угроз, включают учетные записи v2 общего назначения, блоки хранения и учетные записи данных blob. Расширенная защита от угроз доступна во всех общедоступных облаках и правительственных облаках США, но не в других суверенных или правительственных облачных регионах.

Подробная информация о ценах, включая бесплатную 30-дневную пробную версию, смотрите [страницу ценообразования Azure Security Center.](https://azure.microsoft.com/pricing/details/security-center/)


## <a name="set-up-advanced-threat-protection"></a>Настройка расширенной защиты от угроз

Вы можете настроить расширенную защиту от угроз любым из нескольких способов, описанных в следующих разделах.

### <a name="portal"></a>[Портал](#tab/azure-portal)

1. Запуск [портала Azure](https://portal.azure.com/).
1. Перейдите к учетной записи хранения Azure. В **настройках**выберите **Расширенную безопасность.**
1. Выберите ссылку **«Настройки»** на расширенной странице конфигурации безопасности.
1. Установите **Расширенную безопасность** **на ON**.
1. Нажмите **Сохранить,** чтобы сохранить новую или обновленную политику.

    ![Включение Расширенной защиты от угроз для службы хранилища Azure](./media/storage-advanced-threat-protection/storage-advanced-threat-protection-turn-on.png)

### <a name="azure-security-center"></a>[Центр безопасности Azure](#tab/azure-security-center)

При подписке на стандартный уровень в Центре безопасности Azure расширенная защита от угроз автоматически настраивается на все учетные записи хранения данных. Вы можете включить или отключить расширенную защиту угроз для учетных записей хранения данных по определенной подписке следующим образом:

1. Запуск **центра безопасности Azure** на [портале Azure.](https://portal.azure.com)
1. Из основного меню щелкните **настройки цен &.**
1. Нажмите на подписку, которую вы хотите включить, или отключите защиту от угроз для ее учетных записей хранения.

    ![Выбор подписки](./media/storage-advanced-threat-protection/storage-advanced-threat-protection-subscription.png)

1. Нажмите **Уровень ценообразования**.
1. В **разделе «Выберите» уровень ценообразования по типу ресурсов** в строке **учетных записей хранилища** нажмите **«Включено»** или **«Отключено».**

    ![Включить СПС в Центре безопасности](./media/storage-advanced-threat-protection/storage-advanced-threat-protection-pricing2.png)
1. Выберите команду **Сохранить**.

### <a name="template"></a>[Шаблон](#tab/template)

Используйте шаблон менеджера ресурсов Azure для развертывания учетной записи хранилища Azure с расширенной защитой от угроз. Для получения дополнительной информации [см.](https://azure.microsoft.com/resources/templates/201-storage-advanced-threat-protection-create/)

### <a name="azure-policy"></a>[Политика Azure](#tab/azure-policy)

Используйте политику Azure для обеспечения расширенной защиты от угроз в учетных записях хранения данных в определенной группе подписок или ресурса.

1. Запустите страницу Политики Azure **- Определения.**

1. Поиск политики расширенной защиты от угроз в области **учетных записей.**

     ![Политика поиска](./media/storage-advanced-threat-protection/storage-atp-policy-definitions.png)

1. Выберите подписку Azure или группу ресурсов.

    ![Выберите подписку или группу](./media/storage-advanced-threat-protection/storage-atp-policy2.png)

1. Назначай политику.

    ![Страница определения политики](./media/storage-advanced-threat-protection/storage-atp-policy1.png)

### <a name="rest-api"></a>[REST API](#tab/rest-api)

Используйте команды Rest API для создания, обновления или создания расширенной настройки защиты угроз для определенной учетной записи хранения.

* [Расширенная защита от угроз - Создание](https://docs.microsoft.com/rest/api/securitycenter/advancedthreatprotection/create)
* [Расширенная защита от угроз - Получить](https://docs.microsoft.com/rest/api/securitycenter/advancedthreatprotection/get)

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Используйте следующие смдлеты PowerShell:

* [Включить расширенную защиту от угроз](https://docs.microsoft.com/powershell/module/az.security/enable-azsecurityadvancedthreatprotection)
* [Получите расширенную защиту от угроз](https://docs.microsoft.com/powershell/module/az.security/get-azsecurityadvancedthreatprotection)
* [Отключить расширенную защиту от угроз](https://docs.microsoft.com/powershell/module/az.security/disable-azsecurityadvancedthreatprotection)

---

## <a name="explore-security-anomalies"></a>Исследуйте аномалии безопасности

При возникновении аномальных операций с хранилищем вы получите уведомление по электронной почте с информацией о подозрительном событии безопасности. Вы получите следующие сведения о событии:

* Характер аномалии
* имя учетной записи хранения.
* Время мероприятия
* Тип хранилища
* Потенциальные причины
* Следственные действия
* Шаги по исправлению положения

Кроме того, в электронном сообщении приводится информация о возможных причинах, а также рекомендуемые действия по поиску и устранению потенциальной угрозы.

![Расширенная защита от угроз для службы хранилища Azure: оповещение по электронной почте](./media/storage-advanced-threat-protection/storage-advanced-threat-protection-alert-email.png)

Вы можете просматривать и управлять текущими оповещениями безопасности из [плитки оповещения](../../security-center/security-center-managing-and-responding-alerts.md)Центра безопасности Azure Security Center. Если щелкнуть какое-либо оповещение, отобразятся сведения и действия, позволяющие изучить текущую угрозу и устранить будущие угрозы.

![Расширенная защита от угроз для службы хранилища Azure: оповещение по электронной почте](./media/storage-advanced-threat-protection/storage-advanced-threat-protection-alert.png)

## <a name="security-alerts"></a>Оповещения безопасности

Оповещения создаются при нестандартных и потенциально вредоносных попытках получить доступ к учетным записям хранения или использовать их. Список оповещений для хранилища Azure можно узнать в разделе **"Хранение"** в [разделе "Защита данных" в Центре безопасности Azure.](https://docs.microsoft.com/azure/security-center/alerts-reference#alerts-azurestorage)

## <a name="next-steps"></a>Следующие шаги

* Подробнее о [журналах в учетных записях хранилища Azure](/rest/api/storageservices/About-Storage-Analytics-Logging)
* Узнайте больше о [Центре безопасности Azure](../../security-center/security-center-intro.md)
