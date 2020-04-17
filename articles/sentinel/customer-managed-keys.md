---
title: Настройка ключей, управляемых клиентами, в Azure Sentinel Документы Майкрософт
description: Узнайте, как настроить ключи, управляемые клиентами (CMK) в Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/30/2019
ms.author: yelevin
ms.openlocfilehash: 5eed208ed79aeab4e46ed90dd4d340a8b445be96
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81461639"
---
# <a name="set-up-azure-sentinel-customer-managed-key"></a>Настройка ключа, управляемого клиентами Azure Sentinel


В этой статье приводится справочная информация и шаги для настройки ключа, управляемого клиентом (CMK) для Azure Sentinel. CMK позволяет шифровать все данные, сохраненные или отправленные в Azure Sentinel, во всех соответствующих ресурсах хранения с помощью ключа Azure Key Vault, созданного или принадлежащего вам.

> [!NOTE]
> -   Возможность Azure Sentinel CMK предоставляется только **новым** клиентам, и доступ к этой возможности контролируется регистрацией функций Azure.Вы можете запросить azuresentinelCMK@microsoft.comдоступ, связавшись, и по мере наличия емкости ожидающие запросы будут одобрены.
> -   Возможности Azure Sentinel CMK доступны только в восточных районах США, Западной части США и юго-центральной части США.
> -   Возможность CMK доступна только клиентам, отправляя 1 ТБ в день или более. Вы получите информацию о дополнительных ценах, когда подаете заявление в корпорацию Майкрософт на предоставление CMK подписки Azure. Подробнее о зарядке [Log Analytics.](../azure-monitor/platform/customer-managed-keys.md#disclaimers)

## <a name="how-cmk-works"></a>Как работает CMK 

В решении Azure Sentinel используется несколько ресурсов хранения для сбора журналов и функций, включая log Analytics и другие ресурсы хранения. В рамках конфигурации Azure Sentinel CMK вам также придется настроить настройки CMK на соответствующие ресурсы хранения. Данные, сохраненные в ресурсах хранения, не втомая, помимо Log Analytics, также будут зашифрованы.

> [!NOTE]
> Если вы включили CMK на Azure Sentinel, не будет включена любая функция публичного предварительного просмотра, которая не поддерживает CMK.

## <a name="enable-cmk"></a>Включить CMK 

Чтобы предоставить CMK, выполните следующие действия: 

1.  Создайте Хранилище ключей Azure и храните ключ.

2.  Включите CMK в рабочее пространство Log Analytics.

3.  Зарегистрируйтесь в Cosmos DB.

4.  Добавьте политику доступа в экземпляр Azure Key Vault.

5.  Включить CMK в Azure Sentinel.

6.  Включить Azure Sentinel.

### <a name="step-1-create-an-azure-key-vault-and-storing-key"></a>Шаг 1: Создайте убежище ключей Azure и храните ключ

1.  [Создайте ресурс Azure Key Vault,](https://docs.microsoft.com/azure-stack/user/azure-stack-key-vault-manage-portal?view=azs-1910)затем создайте или импортируйте ключ, который будет использоваться для шифрования данных.
    > [!NOTE]
    >  Убежище azure Key Должно быть настроено как восстанавливаемое для защиты ключа и доступа.

1.  [Включите параметры восстановления:](../key-vault/general/best-practices.md#turn-on-recovery-options)

    -   Убедитесь, что [Soft Delete](../key-vault/general/overview-soft-delete.md) включен.

    -   Включите [защиту очистки,](../key-vault/general/overview-soft-delete.md#purge-protection) чтобы защититься от принудительного удаления секрета/хранилища даже после мягкого удаления.

### <a name="step-2-enable-cmk-on-your-log-analytics-workspace"></a>Шаг 2: Включить CMK на рабочем пространстве Log Analytics

Следуйте инструкциям в [конфигурации ключей, управляемой клиентами Azure Monitor,](../azure-monitor/platform/customer-managed-keys.md) чтобы создать рабочее пространство CMK, которое будет использоваться в качестве рабочего пространства Azure Sentinel в следующих шагах.

### <a name="step-3-register-for-cosmos-db"></a>Шаг 3: Зарегистрируйтесь на Cosmos DB

Azure Sentinel работает с Cosmos DB в качестве дополнительного ресурса для хранения данных. Убедитесь в том, чтобы зарегистрироваться в Космос DB.

Следуйте инструкциям Cosmos DB, чтобы зарегистрировать поставщика ресурсов [Azure Cosmos DB](../cosmos-db/how-to-setup-cmk.md#register-resource-provider) для подписки Azure.

### <a name="step-4-add-an-access-policy-to-your-azure-key-vault-instance"></a>Шаг 4: Добавьте политику доступа в экземпляр Azure Key Vault

Убедитесь в том, чтобы добавить доступ из Cosmos DB в экземпляр Azure Key Vault. Следуйте инструкциям Cosmos DB, чтобы [добавить политику доступа к экземпляру Azure Key Vault](../cosmos-db/how-to-setup-cmk.md#add-an-access-policy-to-your-azure-key-vault-instance) с принципом Azure Cosmos DB.

### <a name="step-5-enable-cmk-in-azure-sentinel"></a>Шаг 5: Включить CMK в Azure Sentinel

Возможность Azure Sentinel CMK предоставляется новым клиентам только после получения доступа непосредственно от группы продуктов Azure. Используйте свои контакты в корпорации Майкрософт для получения одобрения от команды Azure Sentinel для включения CMK в ваше решение.

После получения одобрения вам будет предложено предоставить следующую информацию, чтобы включить функцию CMK.

-  Идентификатор рабочего пространства, на котором вы хотите включить CMK

-  Ключевой URL Vault: Копировать ключ "Ключевой идентификатор" до последнего вперед слэш:  
    

    ![идентификатор ключа](./media/customer-managed-keys/key-identifier.png)

    Команда Azure Sentinel позволит функцию Azure Sentinel CMK для предоставленного рабочего пространства.

-  Проверка от группы продуктов Azure Sentinel, которая была одобрена для использования этой функции. Вы должны иметь это, прежде чем продолжить.

### <a name="step-6-enable-azure-sentinel"></a>Шаг 6: Включить Azure Sentinel


Перейдите на портал Azure и включите Azure Sentinel в рабочее пространство, на котором вы настраиваете CMK. Для получения дополнительной [Azure Sentinel Onboarding](quickstart-onboard.md)информации см.

## <a name="key-encryption-key-revocation-or-deletion"></a>Отзыв или удаление ключа шифрования


В случае, если пользователь отменяет ключ шифрования, удалив его или удалив доступ для Azure Sentinel, в течение одного часа Azure Sentinel будет соблюдать изменения и вести себя так, как будто данные больше недоступны. На этом этапе любая выполняемая операция, используюая постоянные ресурсы хранения данных, такие как проглатывание данных, постоянные изменения конфигурации и создание инцидентов, будет предотвращена. Ранее хранящиеся данные не будут удалены, но останутся недоступными. Недоступные данные регулируются политикой хранения данных и будут очищены в соответствии с этой политикой.

Единственной операцией, возможной после того, как ключ шифрования будет отозван или удален, является удаление учетной записи.

Если доступ будет восстановлен после отзыва, Azure Sentinel восстановит доступ к данным в течение часа.

Чтобы узнать больше о том, как [Azure Monitor CMK revocation](../azure-monitor/platform/customer-managed-keys.md#cmk-kek-revocation)это работает в Azure Monitor, см.

## <a name="key-encryption-key-rotation"></a>Поворот ключа шифрования


Аналитики Azure Sentinel и Log Analytics поддерживают поворот ключей. Когда пользователь выполняет вращение ключей в Key Vault, Azure Sentinel поддерживает новый ключ в течение часа.

В Key Vault можно выполнить поворот ключа, создав новую версию ключа:

![ключевое вращение](./media/customer-managed-keys/key-rotation.png)

Предыдущую версию ключа можно отключить через 24 часа или после того, как журналы аудита Azure Key Vault больше не показывают никаких действий, которые используют предыдущую версию.

Если вы используете тот же ключ в Azure Sentinel и в журнале Analytics, необходимо выполнить поворот ключа, необходимо явно обновить ресурс кластера в журнале Analytics с новой ключевой версией Azure Key Vault. Для получения дополнительной [Azure Monitor CMK rotation](../azure-monitor/platform/customer-managed-keys.md#cmk-kek-rotation)информации см.

## <a name="next-steps"></a>Следующие шаги
В этом документе вы узнали, как настроить ключ, управляемый клиентом, в Azure Sentinel. Ознакомьтесь с дополнительными сведениями об Azure Sentinel в соответствующих статьях.
- Узнайте, как [отслеживать свои данные и потенциальные угрозы](quickstart-get-visibility.md).
- Узнайте, как приступить к [обнаружению угроз с помощью Azure Sentinel](tutorial-detect-threats.md).
- [Используйте трудовые книжки](tutorial-monitor-your-data.md) для мониторинга данных.

