---
title: Развертывание служб управления API Azure в нескольких регионах Azure | Документация Майкрософт
description: Дополнительные сведения о развертывании экземпляра службы управления Azure API в различных регионах Azure.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/12/2019
ms.author: apimpm
ms.openlocfilehash: a37df18d91f77dbeb306fd8b028cb14eded812e7
ms.sourcegitcommit: 62bd5acd62418518d5991b73a16dca61d7430634
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/13/2019
ms.locfileid: "68975756"
---
# <a name="how-to-deploy-an-azure-api-management-service-instance-to-multiple-azure-regions"></a>Развертывание экземпляра службы управления Azure API в различных регионах Azure

Служба управления API Azure поддерживает развертывание в нескольких регионах, что позволяет издателям API распространять единую службу управления API Azure через любое количество поддерживаемых регионов Azure. Функция с несколькими регионами позволяет сократить задержку запросов, воспринимаемую географически распространенными потребителями API, и повысить доступность службы, если один регион переходит в режим «вне сети».

Новая служба управления API Azure изначально содержит только одну [единицу][unit] в одном регионе Azure, основном регионе. Дополнительные регионы можно добавить в первичный или дополнительный регион. Компонент шлюза управления API развертывается в каждом выбранном основном и дополнительном регионе. Входящие запросы API автоматически направляются в ближайший регион. Если регион переходит в режим «вне сети», запросы API будут автоматически направляться по неисправному региону на следующий ближайший шлюз.

> [!NOTE]
> Во все регионы развертываются только компоненты шлюза управления API. Компонент управления службами и портал разработчика размещаются только в основном регионе. Поэтому в случае сбоя основного региона доступ к порталу разработчика и возможность изменения конфигурации (например, добавление API-интерфейсов, применение политик) будут ограничены до тех пор, пока основной регион не вернется в режим "в сети". В то время как основной регион доступен в автономном режиме, вторичные регионы продолжат обслуживать трафик API, используя последнюю доступную конфигурацию.

[!INCLUDE [premium.md](../../includes/api-management-availability-premium.md)]

## <a name="add-region"> </a>Создание экземпляра службы управления API в новом регионе

> [!NOTE]
> Если экземпляр службы управления API еще не создан, см. раздел [Создание экземпляра службы управления API][create an api management service instance].

На портале Azure перейдите на страницу **Scale and pricing** (Масштаб и цены) для своего экземпляра службы управления API.

![Вкладка "Масштаб"][api-management-scale-service]

Чтобы развернуть службу в новом регионе, щелкните **+ Add region** (+ Добавить регион) на панели инструментов.

![Добавить регион][api-management-add-region]

Выберите расположение из раскрывающегося списка и задайте число единиц с помощью ползунка.

![Указание единиц][api-management-select-location-units]

Щелкните **Добавить**, чтобы разместить выбранные ресурсы в таблице "Расположения".

Повторите этот процесс, пока не будут настроены все расположения, затем щелкните **Сохранить** на панели инструментов, чтобы начать процесс развертывания.

## <a name="remove-region"> </a>Удаление экземпляра службы управления API из расположения

На портале Azure перейдите на страницу **Scale and pricing** (Масштаб и цены) для своего экземпляра службы управления API.

![Вкладка "Масштаб"][api-management-scale-service]

Для расположения, которое нужно удалить, откройте контекстное меню с помощью кнопки **…** в правой части таблицы. Щелкните **Удалить**.

Подтвердите удаление и нажмите кнопку **Сохранить**, чтобы применить изменения.

## <a name="route-backend"> </a>Маршрутизация вызовов API в региональных серверных службах

Служба управления API Azure поддерживает только один URL-адрес внутренней службы. Несмотря на то что экземпляры службы управления API Azure существуют в разных регионах, шлюз API будет по-прежнему перенаправлять запросы к одной и той же внутренней службе, которая развертывается только в один регион. В этом случае выигрыш в производительности будет поступать только из ответов, кэшированных в службе управления API Azure в регионе, указанном для запроса, но обращение к серверной части по всему миру может по-прежнему вызывать большую задержку.

Чтобы полностью использовать географическое распределение системы, необходимо обладать серверными службами, развернутыми в тех же регионах, что и экземпляры службы управления API Azure. Затем с помощью политик и свойства `@(context.Deployment.Region)` можно перенаправлять трафик в локальные экземпляры серверной части.

1. Перейдите к экземпляру службы управления API Azure и в меню слева щелкните **Интерфейсы API**.
2. Выберите нужный API.
3. Щелкните **Редактор кода** в раскрывающемся меню со стрелкой в **Обработке входящих запросов**.

    ![Редактор кода API](./media/api-management-howto-deploy-multi-region/api-management-api-code-editor.png)

4. Используйте условие `set-backend` в сочетании с условными политиками `choose`, чтобы создать подходящую политику маршрутизации в разделе `<inbound> </inbound>` файла.

    Например, следующий файл XML будет работать как для региона "Западная часть США", так и для региона "Восточная Азия".

    ```xml
    <policies>
        <inbound>
            <base />
            <choose>
                <when condition="@("West US".Equals(context.Deployment.Region, StringComparison.OrdinalIgnoreCase))">
                    <set-backend-service base-url="http://contoso-us.com/" />
                </when>
                <when condition="@("East Asia".Equals(context.Deployment.Region, StringComparison.OrdinalIgnoreCase))">
                    <set-backend-service base-url="http://contoso-asia.com/" />
                </when>
                <otherwise>
                    <set-backend-service base-url="http://contoso-other.com/" />
                </otherwise>
            </choose>
        </inbound>
        <backend>
            <base />
        </backend>
        <outbound>
            <base />
        </outbound>
        <on-error>
            <base />
        </on-error>
    </policies>
    ```

> [!TIP]
> Вы также можете переключить серверные службы с помощью [диспетчера трафика Azure](https://azure.microsoft.com/services/traffic-manager/), направить вызовы API в диспетчер трафика и разрешить маршрутизацию автоматически.

## <a name="custom-routing"> </a>Использование настраиваемой маршрутизации для региональных ШЛЮЗОВ управления API

Управление API направляет запросы на региональный _шлюз_ в соответствии с [наименьшей задержкой](../traffic-manager/traffic-manager-routing-methods.md#performance). Хотя этот параметр нельзя переопределить в службе управления API, можно использовать собственный диспетчер трафика с пользовательскими правилами маршрутизации.

1. Создайте собственный [диспетчер трафика Azure](https://azure.microsoft.com/services/traffic-manager/).
1. Если вы используете личный домен, [используйте его с диспетчером трафика](../traffic-manager/traffic-manager-point-internet-domain.md) вместо службы управления API.
1. [Настройте региональные конечные точки управления API в диспетчере трафика](../traffic-manager/traffic-manager-manage-endpoints.md). Региональные конечные точки соответствуют шаблону `https://<service-name>-<region>-01.regional.azure-api.net`URL-адреса, например. `https://contoso-westus2-01.regional.azure-api.net`
1. [Настройка конечных точек регионального состояния управления API в диспетчере трафика](../traffic-manager/traffic-manager-monitoring.md). Конечные точки регионального состояния следуют шаблону `https://<service-name>-<region>-01.regional.azure-api.net/status-0123456789abcdef`URL-адреса, например. `https://contoso-westus2-01.regional.azure-api.net/status-0123456789abcdef`
1. Укажите [метод маршрутизации](../traffic-manager/traffic-manager-routing-methods.md) диспетчера трафика.

[api-management-management-console]: ./media/api-management-howto-deploy-multi-region/api-management-management-console.png
[api-management-scale-service]: ./media/api-management-howto-deploy-multi-region/api-management-scale-service.png
[api-management-add-region]: ./media/api-management-howto-deploy-multi-region/api-management-add-region.png
[api-management-select-location-units]: ./media/api-management-howto-deploy-multi-region/api-management-select-location-units.png
[api-management-remove-region]: ./media/api-management-howto-deploy-multi-region/api-management-remove-region.png
[create an api management service instance]: get-started-create-service-instance.md
[get started with azure api management]: get-started-create-service-instance.md
[deploy an api management service instance to a new region]: #add-region
[delete an api management service instance from a region]: #remove-region
[unit]: https://azure.microsoft.com/pricing/details/api-management/
[premium]: https://azure.microsoft.com/pricing/details/api-management/
