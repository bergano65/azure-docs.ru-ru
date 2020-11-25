---
author: memildin
ms.author: memildin
manager: rkarlin
ms.date: 11/22/2020
ms.topic: include
ms.openlocfilehash: 30de9181fd23a29b28973d01899f0b23fca3ae89
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/24/2020
ms.locfileid: "95530002"
---
## <a name="availability"></a>Доступность

|Аспект|Сведения|
|----|:----|
|Состояние выпуска:|Общедоступная версия (GA)|
|Цены|Плата за использование **Azure Defender для реестров контейнеров** начисляется по тарифам, приведенным на [странице с ценами](../articles/security-center/security-center-pricing.md).|
|Поддерживаемые реестры и образы:|Образы Linux в реестрах ACR, доступные из общедоступного Интернета через оболочку.|
|Неподдерживаемые реестры и образы:|Образы Windows<br>"Частные" реестры.<br>Реестры с доступом, ограниченным брандмауэром, конечной точкой службы или частными конечными точками, например Приватным каналом Azure.<br>Сверхминималистические образы, например [временные образы Docker](https://hub.docker.com/_/scratch/) или "бездистрибутивные" образы, которые содержат только приложение и его зависимости для среды выполнения без диспетчера пакетов, оболочки или ОС.|
|Требуемые роли и разрешения|**Читатель сведений о безопасности**, а также [роли и разрешения Реестра контейнеров Azure](../articles/container-registry/container-registry-roles.md).|
|Облако.|:::image type="icon" source="../articles/security-center/media/icons/yes-icon.png" border="false"::: Коммерческие облака<br>:::image type="icon" source="../articles/security-center/media/icons/yes-icon.png" border="false"::: US Gov — в настоящее время поддерживается только функция проверки или принудительной отправки. Подробные сведения см. в статье [Когда проверяются образы?](../articles/security-center/defender-for-container-registries-introduction.md#when-are-images-scanned)<br>:::image type="icon" source="../articles/security-center/media/icons/no-icon.png" border="false"::: China Gov и другие правительственные облака|
|||