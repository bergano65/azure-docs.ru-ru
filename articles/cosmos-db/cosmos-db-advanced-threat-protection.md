---
title: Расширенная защита от угроз для Azure Cosmos DB
description: Узнайте, как Azure Cosmos DB обеспечивает шифрование неактивных данных и их реализацию.
author: monhaber
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/24/2019
ms.author: v-mohabe
ms.custom: seodec18
ms.openlocfilehash: 02281a1cad9c7e6f9680441a699fa5d34558b890
ms.sourcegitcommit: a0b37e18b8823025e64427c26fae9fb7a3fe355a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/25/2019
ms.locfileid: "68501498"
---
# <a name="advanced-threat-protection-for-azure-cosmos-db"></a>Расширенная защита от угроз для Azure Cosmos DB

Расширенная защита от угроз для Azure Cosmos DB предоставляет дополнительный уровень логики безопасности, который обнаруживает необычные и потенциально опасные попытки доступа или использования учетных записей Azure Cosmos DB. Этот уровень защиты позволяет решать угрозы даже без эксперта по безопасности и интегрировать их с системами мониторинга централизованной безопасности.

Оповещения системы безопасности активируются при возникновении аномалий в действии. Эти оповещения системы безопасности интегрируются с  [центром безопасности Azure](https://azure.microsoft.com/services/security-center/), а также отправляются по электронной почте администраторам подписки с подробными сведениями о подозрительных действиях и рекомендациями по исследованию и исправлению угроз.

> [!NOTE]
>
> * Расширенная защита от угроз для Azure Cosmos DB в настоящее время доступна только для API SQL.
> * Расширенная защита от угроз для Azure Cosmos DB в настоящее время доступна в облачных регионах Azure для государственных организаций и независимых.

Для полного исследования оповещений системы безопасности рекомендуется включить [ведение журнала диагностики в Azure Cosmos DB, в](https://docs.microsoft.com/azure/cosmos-db/logging)котором регистрируются операции с самой базой данных, включая операции CRUD для всех документов, контейнеров и баз данных.

## <a name="set-up-advanced-threat-protection"></a>Настройка расширенной защиты от угроз

### <a name="set-up-atp-using-the-portal"></a>Настройка ATP с помощью портала

1. Запустите портал Azure по адресу  [https://portal.azure.com](https://portal.azure.com/).

2. В учетной записи Azure Cosmos DB в меню **Параметры** выберите пункт **Расширенная безопасность**.

    ![Настройка ATP](./media/cosmos-db-advanced-threat-protection/cosmos-db-atp.png)

3. В колонке Расширенная конфигурация **безопасности** :

    * Выберите параметр **Advanced Threat protection** ( **вкл**.), чтобы включить его.
    * Нажмите кнопку **сохранить** , чтобы сохранить новую или обновленную политику Advanced Threat protection.   

### <a name="set-up-atp-using-rest-api"></a>Настройка ATP с помощью REST API

Используйте команды API-интерфейса для создания, обновления или получения параметра Advanced Threat Protection для конкретной учетной записи Azure Cosmos DB.

* [Расширенная защита от угроз — создание](https://go.microsoft.com/fwlink/?linkid=2099745)
* [Расширенная защита от угроз — получение](https://go.microsoft.com/fwlink/?linkid=2099643)

### <a name="set-up-atp-using-azure-powershell"></a>Настройка ATP с помощью Azure PowerShell

Используйте следующие командлеты PowerShell:

* [Включить расширенную защиту от угроз](https://go.microsoft.com/fwlink/?linkid=2099607&clcid=0x409)
* [Получить расширенную защиту от угроз](https://go.microsoft.com/fwlink/?linkid=2099608&clcid=0x409)
* [Отключить Advanced Threat protection](https://go.microsoft.com/fwlink/?linkid=2099709&clcid=0x409)

## <a name="manage-atp-security-alerts"></a>Управление оповещениями системы безопасности ATP

При возникновении Azure Cosmos DB аномалий действий создается оповещение системы безопасности с информацией о подозрительной событии безопасности. 

 В центре безопасности Azure можно просматривать текущие [оповещения системы безопасности](../security-center/security-center-alerts-overview.md)и управлять ими.  Щелкните определенное оповещение в [центре безопасности](https://ms.portal.azure.com/#blade/Microsoft_Azure_Security/SecurityMenuBlade/0) , чтобы просмотреть возможные причины и рекомендуемые действия для изучения и устранения потенциальных угроз. На следующем рисунке показан пример сведений о предупреждении, предоставленных в центре безопасности.

 ![Сведения об угрозе](./media/cosmos-db-advanced-threat-protection/cosmos-db-alert-details.png)

Также отправляется уведомление по электронной почте с подробными сведениями о предупреждении и рекомендуемыми действиями. На следующем рисунке показан пример электронного сообщения с оповещением.

 ![Подробности об оповещении](./media/cosmos-db-advanced-threat-protection/cosmos-db-alert.png)

## <a name="cosmos-db-atp-alerts"></a>Оповещения Cosmos DB ATP

 Список предупреждений, созданных при наблюдении за учетными записями Azure Cosmos DB, см. в разделе [Cosmos DB Alerts](../security-center/security-center-alerts-data-services.md#cosmos-db) в документации по центру безопасности.

## <a name="next-steps"></a>Следующие шаги

* Дополнительные сведения о [ведении журналов диагностики в Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/logging#turn-on-logging-in-the-azure-portal)
* Дополнительные сведения о [центре безопасности Azure](https://docs.microsoft.com/azure/security-center/security-center-intro)