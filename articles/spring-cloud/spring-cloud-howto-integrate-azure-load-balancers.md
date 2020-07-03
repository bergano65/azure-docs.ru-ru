---
title: Руководство. Интеграция Azure Веснного облака с решениями балансировки нагрузки Azure
description: Интеграция Azure Веснного облака с решениями балансировки нагрузки Azure
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: how-to
ms.date: 04/20/2020
ms.openlocfilehash: 7022c4587b425168fc5bd2182ed65c281633aabf
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/27/2020
ms.locfileid: "82177085"
---
# <a name="integrate-azure-spring-cloud-with-azure-load-balance-solutions"></a>Интеграция Azure Веснного облака с решениями балансировки нагрузки Azure

Azure Веснное облако поддерживает микрослужбы в Azure.  Для повышения бизнеса может потребоваться несколько центров обработки данных с управлением несколькими экземплярами Azure Веснного облака.

Azure уже предоставляет различные решения балансировки нагрузки. Существует три варианта интеграции Azure Веснного облака с решениями балансировки нагрузки Azure.

1.  Интеграция Azure Веснного облака с диспетчером трафика Azure
2.  Интеграция Azure Веснного облака с шлюзом приложений Azure
3.  Интеграция Azure Веснного облака с помощью передней дверцы Azure

## <a name="prerequisites"></a>Предварительные требования

* Azure Веснного облака: [как создать облачную службу Azure весны](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-quickstart-launch-app-portal)
* Диспетчер трафика Azure: [Создание диспетчера трафика](https://docs.microsoft.com/azure/traffic-manager/quickstart-create-traffic-manager-profile/)
* Шлюз приложений Azure: [Создание шлюза приложения](https://docs.microsoft.com/azure/application-gateway/quick-create-portal)
* Передняя дверца Azure: [Создание передней дверцы](https://docs.microsoft.com/azure/frontdoor/quickstart-create-front-door)

## <a name="integrate-azure-spring-cloud-with-azure-traffic-manager"></a>Интеграция Azure Веснного облака с диспетчером трафика Azure

Чтобы интегрировать Azure веснного облака с диспетчером трафика, добавьте общедоступные конечные точки в качестве конечных точек диспетчера трафика, а затем настройте личный домен для диспетчера трафика и Azure веснного облака.

### <a name="add-endpoint-in-traffic-manager"></a>Добавление конечной точки в диспетчере трафика
Добавление конечных точек в диспетчере трафика:
1.  Укажите **тип** для *внешней конечной точки*.
1.  Введите полное доменное имя (FQDN) каждой общедоступной конечной точки Azure весны облака.
1. Нажмите кнопку **ОК**.

    ![Диспетчер трафика 1](media/spring-cloud-load-balancers/traffic-manager-1.png) ![диспетчера трафика 2](media/spring-cloud-load-balancers/traffic-manager-2.png)

### <a name="configure-custom-domain"></a>Настроить личный домен
Чтобы завершить настройку, выполните следующие действия.
1.  Войдите на веб-сайт поставщика домена и создайте сопоставление записи CNAME из личного домена в доменное имя Azure по умолчанию диспетчера трафика.
1.  Следуйте инструкциям, [чтобы добавить личный домен в Azure веснного облака](spring-cloud-tutorial-custom-domain.md).
1. Добавьте приведенную выше привязку пользовательского домена к диспетчеру трафика в Azure веснного облака, соответствующей службе приложений, и отправьте сертификат SSL.

    ![Диспетчер трафика 3](media/spring-cloud-load-balancers/traffic-manager-3.png)

## <a name="integrate-azure-spring-cloud-with-azure-app-gateway"></a>Интеграция Azure Веснного облака с шлюзом приложений Azure

Для интеграции с облачной службой Azure весны выполните следующие настройки.

### <a name="configure-backend-pool"></a>Настройка внутреннего пула
1. Укажите **тип целевого объекта** : *IP-адрес* или *полное доменное имя*.
1. Введите общедоступные конечные точки Azure веснного облака.

    ![Шлюз приложений 1](media/spring-cloud-load-balancers/app-gateway-1.png)

### <a name="add-custom-probe"></a>Добавить пользовательскую проверку
1. Выберите **зонды работоспособности** , а затем **Добавить** в диалоговое окно "открыть пользовательскую **проверку** ". 
1. Ключевой момент — выбрать *Да* для параметра **выбрать имя узла из серверных параметров HTTP** .

    ![Шлюз приложений 2](media/spring-cloud-load-balancers/app-gateway-2.png)

### <a name="configure-http-setting"></a>Настройка параметра HTTP
1.  Выберите **Параметры HTTP** , а затем **Добавить** , чтобы добавить параметр HTTP.
1.  **Переопределить на новое имя узла:** выберите *Да*.
1.  **Переопределение имени узла**: выберите выбрать **имя узла из серверного целевого объекта**.
1.  **Использовать пользовательскую пробу**: выберите *Да* и выберите пользовательскую пробную версию, созданную выше.

    ![Шлюз приложений 3](media/spring-cloud-load-balancers/app-gateway-3.png)

## <a name="integrate-azure-spring-cloud-with-azure-front-door"></a>Интеграция Azure Веснного облака с помощью передней дверцы Azure

Чтобы выполнить интеграцию с облачной службой Azure весны и настроить серверный пул, 
1. **Добавление внутреннего пула**.
1. Укажите конечную точку серверной части, добавив узел.

    ![Передняя дверь 1](media/spring-cloud-load-balancers/front-door-1.png)

1.  Укажите **тип внутреннего узла** в качестве *пользовательского узла*.
1.  Введите полное доменное имя общедоступных конечных точек облачной службы Azure весны в **имени узла внутреннего сервера**.
1.  Примите **заголовок узла внутреннего** сервера по умолчанию, который совпадает с **именем внутреннего узла**.

    ![Передняя дверь 2](media/spring-cloud-load-balancers/front-door-2.png)

## <a name="next-steps"></a>Следующие шаги
* [Создание диспетчера трафика](https://docs.microsoft.com/azure/traffic-manager/quickstart-create-traffic-manager-profile/)
* [Создание шлюза приложений](https://docs.microsoft.com/azure/application-gateway/quick-create-portal)
* [Создание передней дверцы](https://docs.microsoft.com/azure/frontdoor/quickstart-create-front-door)
