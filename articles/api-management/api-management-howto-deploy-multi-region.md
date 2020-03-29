---
title: Развертывание служб управления API Azure в нескольких регионах Azure
titleSuffix: Azure API Management
description: Дополнительные сведения о развертывании экземпляра службы управления Azure API в различных регионах Azure.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 08/12/2019
ms.author: apimpm
ms.openlocfilehash: 5c71f37741de06b8633e7eafaae2f29823214f74
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75442668"
---
# <a name="how-to-deploy-an-azure-api-management-service-instance-to-multiple-azure-regions"></a>Развертывание экземпляра службы управления Azure API в различных регионах Azure

Управление API Azure поддерживает многорегионнее развертывание, что позволяет издателям API распространять одну службу управления API Azure в любых числах поддерживаемых регионов Azure. Функция Multi-region помогает уменьшить задержку запроса, воспринимаемую географически распределенными потребителями API, и повышает доступность услуг, если один регион переходит в автономный режим.

Новая служба управления API Azure первоначально содержит только одну [единицу][unit] в одном регионе Azure — регионе Первоначального общества. Дополнительные регионы могут быть добавлены к первичным или средним регионам. Компонент шлюза Управления API развертывается в каждом выбранном регионе Начального и Вторичного. Входящие запросы API автоматически направляются в ближайший регион. Если регион переходит в автономный режим, запросы API будут автоматически направляться по сбойву в несевом регионе к следующему близкому шлюзу.

> [!NOTE]
> Только компонент шлюза Управления API развертывается во всех регионах. Компонент управления сервисом и портал разработчиков размещаются только в регионе Первоначального общества. Таким образом, в случае простоя региона Первоначального общества доступ к порталу разработчика и возможность изменения конфигурации (например, добавление AIS, применение политик) будут нарушены до тех пор, пока регион Первоначального общества не вернется в строй. В то время как регион Первоначального общества находится в автономном режиме, доступные вторичные регионы, будут продолжать обслуживать трафик API, используя последнюю конфигурацию, доступную для них.

[!INCLUDE [premium.md](../../includes/api-management-availability-premium.md)]

## <a name="deploy-an-api-management-service-instance-to-a-new-region"></a><a name="add-region"> </a>Создание экземпляра службы управления API в новом регионе

> [!NOTE]
> Если экземпляр службы управления API еще не создан, выполните инструкции в статье [Создание экземпляра службы управления API Azure][create an api management service instance].

На портале Azure перейдите на страницу **Scale and pricing** (Масштаб и цены) для своего экземпляра службы управления API.

![Вкладка "Масштаб"][api-management-scale-service]

Чтобы развернуть службу в новом регионе, щелкните **+ Add region** (+ Добавить регион) на панели инструментов.

![Добавление региона][api-management-add-region]

Выберите расположение из раскрывающегося списка и задайте число единиц с помощью ползунка.

![Указание единиц][api-management-select-location-units]

Щелкните **Добавить**, чтобы разместить выбранные ресурсы в таблице "Расположения".

Повторите этот процесс, пока не будут настроены все расположения, затем щелкните **Сохранить** на панели инструментов, чтобы начать процесс развертывания.

## <a name="delete-an-api-management-service-instance-from-a-location"></a><a name="remove-region"> </a>Удаление экземпляра службы управления API из расположения

На портале Azure перейдите на страницу **Scale and pricing** (Масштаб и цены) для своего экземпляра службы управления API.

![Вкладка "Масштаб"][api-management-scale-service]

Для расположения, которое нужно удалить, откройте контекстное меню с помощью кнопки **…** в правой части таблицы. Щелкните **Удалить**.

Подтвердите удаление и нажмите кнопку **Сохранить**, чтобы применить изменения.

## <a name="route-api-calls-to-regional-backend-services"></a><a name="route-backend"> </a>Маршрутизация вызовов API в региональных серверных службах

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
> Вы также можете передать свои бэкэнд-сервисы менеджеру [azure Traffic Manager,](https://azure.microsoft.com/services/traffic-manager/)направить вызовы API менеджеру трафика и позволить ему решить возможность автоматического устранения трассировки.

## <a name="use-custom-routing-to-api-management-regional-gateways"></a><a name="custom-routing"> </a>Используйте пользовательскую трассу для региональных шлюзов Управления API

API Management направляет запросы в региональный _шлюз_ на основе [наименьшей задержки.](../traffic-manager/traffic-manager-routing-methods.md#performance) Несмотря на то, что в Управлении API невозможно переопределить эту настройку, вы можете использовать свой собственный диспетчер трафика с пользовательскими правилами трассировки.

1. Создайте свой собственный [менеджер трафика Azure.](https://azure.microsoft.com/services/traffic-manager/)
1. Если вы используете пользовательский домен, [используйте его с диспетчером трафика,](../traffic-manager/traffic-manager-point-internet-domain.md) а не службой управления API.
1. [Нанастройка региональных конечных точек управления API в диспетчере трафика.](../traffic-manager/traffic-manager-manage-endpoints.md) Региональные конечные точки следуют `https://<service-name>-<region>-01.regional.azure-api.net`шаблону `https://contoso-westus2-01.regional.azure-api.net`URL, например.
1. [Нанастройка региональных конечных точек aPI Management в диспетчерской трафика.](../traffic-manager/traffic-manager-monitoring.md) Конечные точки регионального статуса `https://<service-name>-<region>-01.regional.azure-api.net/status-0123456789abcdef`следуют `https://contoso-westus2-01.regional.azure-api.net/status-0123456789abcdef`шаблону URL, например.
1. Укажите [метод разгрома](../traffic-manager/traffic-manager-routing-methods.md) диспетчера трафика.

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
