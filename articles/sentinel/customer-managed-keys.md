---
title: Настройка ключей, управляемых клиентом, в Azure Sentinel | Документация Майкрософт
description: Узнайте, как настроить ключи, управляемые клиентом (CMK) в Azure Sentinel.
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
ms.openlocfilehash: bc2fa02925e7f5c671085eb87ca0431d3fca7691
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/25/2020
ms.locfileid: "77587963"
---
# <a name="set-up-azure-sentinel-customer-managed-key"></a>Настройка ключа, управляемого клиентом Azure Sentinel


В этой статье содержатся общие сведения и инструкции по настройке ключа, управляемого клиентом (CMK), для Azure Sentinel. CMK позволяет шифровать все данные, сохраненные или отправленные в метку Azure, во все соответствующие ресурсы хранилища с помощью Azure Key Vaultного ключа, созданного или принадлежащего вам.

> [!NOTE]
> -   Функция Azure Sentinel CMK предоставляется только тем клиентам, которые являются **новыми** , и доступ к этой возможности контролируется с помощью регистрации компонентов Azure. Вы можете запросить доступ, обратившись в azuresentinelCMK@microsoft.com, и по мере доступности емкости ожидающие запросы будут утверждены.
> -   Функция Azure Sentinel CMK доступна только в восточной части США, западной части США 2 и в юго-центральном регионе США.
> -   Возможность CMK доступна только для клиентов, отправляющих 1 ТБ в день или более. Вы получите сведения о дополнительных ценах, когда будете применены к Майкрософт для подготовки CMK к подписке Azure. Дополнительные сведения о [log Analytics](../azure-monitor/platform/customer-managed-keys.md#disclaimers) зарядки.

## <a name="how-cmk-works"></a>Как работает CMK 

Решение Sentinel Azure использует несколько ресурсов хранилища для сбора и функций журналов, включая Log Analytics и другие ресурсы хранилища. В рамках настройки CMK Azure Sentinel необходимо также настроить параметры CMK для связанных ресурсов хранилища. Данные, сохраненные в ресурсах хранилища, кроме Log Analytics, также шифруются.

> [!NOTE]
> Если включить CMK в Azure Sentinel, то любая общедоступная Предварительная версия функции, которая не поддерживает CMK, не будет включена.

## <a name="enable-cmk"></a>Включить CMK 

Чтобы подготавливать CMK, выполните следующие действия. 

1.  Создание Azure Key Vault и хранение ключа.

2.  Включите CMK в рабочей области Log Analytics.

3.  Зарегистрируйтесь для Cosmos DB.

4.  Добавьте политику доступа к экземпляру Azure Key Vault.

5.  Включите CMK в Azure Sentinel.

6.  Включите метку Azure.

### <a name="step-1-create-an-azure-key-vault-and-storing-key"></a>Шаг 1. Создание Azure Key Vault и хранение ключа

1.  [Создайте Azure Key Vault ресурс](https://docs.microsoft.com/azure-stack/user/azure-stack-key-vault-manage-portal?view=azs-1910), а затем создайте или импортируйте ключ, который будет использоваться для шифрования данных.
    > [!NOTE]
    >  Azure Key Vault должны быть настроены как восстанавливаемые для защиты ключа и доступа.

1.  [Включите параметры восстановления.](../key-vault/key-vault-best-practices.md#turn-on-recovery-options)

    -   Убедитесь, что [обратимое удаление](../key-vault/key-vault-ovw-soft-delete.md) включено.

    -   Включите [защиту от очистки](../key-vault/key-vault-ovw-soft-delete.md#purge-protection) , чтобы защититься от принудительного удаления секрета или хранилища даже после обратимого удаления.

### <a name="step-2-enable-cmk-on-your-log-analytics-workspace"></a>Шаг 2. Включение CMK в рабочей области Log Analytics

Следуйте инструкциям в разделе [Azure Monitor управляемой клиентом конфигурации ключа](../azure-monitor/platform/customer-managed-keys.md) , чтобы создать рабочую область CMK, которая будет использоваться в качестве рабочей области Azure Sentinel в следующих шагах.

### <a name="step-3-register-for-cosmos-db"></a>Шаг 3. Регистрация для Cosmos DB

Azure Sentinel работает с Cosmos DB в качестве дополнительного ресурса хранилища. Обязательно выполните регистрацию в Cosmos DB.

Выполните инструкции Cosmos DB, чтобы [зарегистрировать](../cosmos-db/how-to-setup-cmk.md#register-resource-provider) поставщик ресурсов Azure Cosmos DB для подписки Azure.

### <a name="step-4-add-an-access-policy-to-your-azure-key-vault-instance"></a>Шаг 4. Добавление политики доступа к экземпляру Azure Key Vault

Не забудьте добавить доступ из Cosmos DB к экземпляру Azure Key Vault. Следуйте инструкциям Cosmos DB, чтобы [Добавить политику доступа к экземпляру Azure Key Vault](../cosmos-db/how-to-setup-cmk.md#add-an-access-policy-to-your-azure-key-vault-instance) с помощью Azure Cosmos DB участника.

### <a name="step-5-enable-cmk-in-azure-sentinel"></a>Шаг 5. Включение CMK в Azure Sentinel

Функция Azure Sentinel CMK предоставляется новым клиентам только после получения доступа непосредственно из группы продуктов Azure. Используйте свои контакты в корпорации Майкрософт, чтобы получить утверждение от команды Azure Sentinel, чтобы включить CMK в решении.

После получения утверждения вам будет предложено предоставить следующие сведения, чтобы включить функцию CMK.

-  ИДЕНТИФИКАТОР рабочей области, в которой необходимо включить CMK

-  URL-адрес Key Vault: Скопируйте "идентификатор ключа" ключа до последней косой черты:  
    

    ![Идентификатор ключа](./media/customer-managed-keys/key-identifier.png)

    Команда Azure Sentinel позволяет включить функцию Sentinel CMK для указанной рабочей области.

-  Проверка у команды продукта Sentinel Azure, которую вы утвердили для использования этой функции. Это необходимо сделать перед продолжением.

### <a name="step-6-enable-azure-sentinel"></a>Шаг 6. Включение Sentinel Azure


Перейдите на портал Azure и включите метку Azure в рабочей области, в которой вы настроили CMK. Дополнительные сведения см. в статье о подключении [Sentinel в Azure](quickstart-onboard.md).

## <a name="key-encryption-key-revocation-or-deletion"></a>Отзыв ключа шифрования ключа или удаление


В случае, если пользователь отменяет ключ шифрования ключа, удаляя его или удаляя доступ для Sentinel Azure в течение одного часа, Azure Sentinel будет учитывать изменения и вести себя, как если бы данные больше не были доступны. На этом этапе все выполняемые операции, использующие постоянные ресурсы хранилища, такие как прием данных, постоянные изменения конфигурации и создание инцидентов, будут предотвращены. Ранее сохраненные данные не будут удалены, но останутся недоступными. Недоступные данные определяются политикой хранения данных и удаляются в соответствии с этой политикой.

Единственной операцией, возможной после отмены или удаления ключа шифрования, является удаление учетной записи.

Если доступ восстанавливается после отзыва, Azure Sentinel будет восстанавливать доступ к данным в течение часа.

Дополнительные сведения о том, как это работает в Azure Monitor, см. в разделе [Azure Monitor CMK отзыва](../azure-monitor/platform/customer-managed-keys.md#cmk-kek-revocation).

## <a name="key-encryption-key-rotation"></a>Смена ключа шифрования ключей


Смена ключа поддержки Sentinel и Log Analytics в Azure. Когда пользователь выполняет смену ключей в Key Vault, Azure Sentinel поддерживает новый ключ в течение часа.

В Key Vault можно выполнить смену ключей, создав новую версию ключа.

![смена ключа](./media/customer-managed-keys/key-rotation.png)

Предыдущую версию ключа можно отключить через 24 часа или после того, как Azure Key Vault журналы аудита больше не будут отображать действия, использующие предыдущую версию.

Если вы используете один и тот же ключ в Azure Sentinel и в Log Analytics, необходимо явным образом обновить ресурс кластера в Log Analytics с помощью новой версии ключа Azure Key Vault. Дополнительные сведения см. в разделе [Azure Monitor CMKный поворот](../azure-monitor/platform/customer-managed-keys.md#cmk-kek-rotation).

## <a name="next-steps"></a>Следующие шаги
В этом документе вы узнали, как настроить ключ, управляемый клиентом, в Azure Sentinel. Ознакомьтесь с дополнительными сведениями об Azure Sentinel в соответствующих статьях.
- Узнайте, как [отслеживать свои данные и потенциальные угрозы](quickstart-get-visibility.md).
- Узнайте, как приступить к [обнаружению угроз с помощью Azure Sentinel](tutorial-detect-threats.md).
- [Используйте книги](tutorial-monitor-your-data.md) для отслеживания данных.

