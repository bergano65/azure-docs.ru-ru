---
title: Защитник Azure для Key Vault — преимущества и возможности
description: Узнайте о преимуществах и возможностях защитника Azure для Key Vault.
author: memildin
ms.author: memildin
ms.date: 9/22/2020
ms.topic: conceptual
ms.service: security-center
ms.custom: references_regions
manager: rkarlin
ms.openlocfilehash: 78a522922f8580003e2d2ff588cbf10dbf5fff9d
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/25/2020
ms.locfileid: "91301777"
---
# <a name="introduction-to-azure-defender-for-key-vault"></a>Общие сведения о защитнике Azure для Key Vault

Azure Key Vault — это облачная служба, которая обеспечивает защиту ключей шифрования и секретов (например, сертификатов, строк подключения и паролей). 

Включите **защитник Azure для Key Vault** для Azure — собственная защита от угроз для Azure Key Vault, предоставляя дополнительный уровень анализа безопасности. 

## <a name="availability"></a>Доступность

|Аспект|Подробности|
|----|:----|
|Состояние выпуска:|Общедоступная версия (GA)|
|Цены|Плата **за использование защитника Azure для Key Vault** оплачивается, как показано на [странице с ценами](security-center-pricing.md) .|
|Облако.|![Да](./media/icons/yes-icon.png) Коммерческие облака<br>![Нет](./media/icons/no-icon.png) National/независимых (US Gov, Китай gov, другое gov)|
|||

## <a name="what-are-the-benefits-of-azure-defender-for-key-vault"></a>Каковы преимущества защитника Azure для Key Vault?

Защитник Azure обнаруживает необычные и потенциально опасные попытки доступа или использования учетных записей Key Vault. Этот уровень защиты позволяет устранять угрозы даже при отсутствии экспертных знаний по безопасности и избавляет от необходимости управлять сторонними системами мониторинга безопасности.  

При возникновении аномальных действий защитник Azure отображает предупреждения и при необходимости отправляет их по электронной почте соответствующим участникам Организации. Эти оповещения содержат сведения о подозрительных действиях и рекомендации о том, как определить причину угроз и устранить сами угрозы. 

## <a name="azure-defender-for-key-vault-alerts"></a>Оповещения Azure Defender для Key Vault
При получении оповещения от защитника Azure для Key Vault мы рекомендуем исследовать и реагировать на это предупреждение, как описано в разделе [реагирование на защитник Azure для Key Vault](defender-for-key-vault-usage.md). Защитник Azure для Key Vault защищает приложения и учетные данные, поэтому даже если вы знакомы с приложением или пользователем, вызвавшим оповещение, важно проверить ситуацию, окружающую каждое оповещение.

Оповещения отображаются на странице **безопасность** Key Vault, на панели мониторинга защитника Azure и на странице оповещений центра безопасности.

:::image type="content" source="./media/defender-for-key-vault-intro/key-vault-security-page.png" alt-text="Страница безопасности Azure Key Vault":::


> [!TIP]
> Вы можете имитировать службу "защитник Azure" для Key Vault предупреждений, следуя инструкциям в разделе [Проверка обнаружения угроз Azure Key Vault в центре безопасности Azure](https://techcommunity.microsoft.com/t5/azure-security-center/validating-azure-key-vault-threat-detection-in-azure-security/ba-p/1220336).


## <a name="next-steps"></a>Дальнейшие действия

В этой статье вы узнали о защитнике Azure для Key Vault.

Связанные материалы см. в следующих статьях: 

- [Key Vault оповещениях системы безопасности](alerts-reference.md#alerts-azurekv)— Key Vault раздел ссылочной таблицы для всех оповещений центра безопасности Azure.
- [Экспорт оповещений в SIEM](continuous-export.md)
- [Отключение оповещений от защитника Azure](alerts-suppression-rules.md)