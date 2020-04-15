---
title: Создание шлюза приложения Azure - Классический CLI Azure
description: Создание Шлюза приложений с помощью классического Azure CLI в Resource Manager
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: conceptual
ms.date: 4/15/2019
ms.author: victorh
ms.openlocfilehash: ed7bbbf55956d655115b94c8ac46432b3e36c49b
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/14/2020
ms.locfileid: "81312630"
---
# <a name="create-an-application-gateway-by-using-the-azure-cli"></a>Создание шлюза приложений с помощью интерфейса командной строки Azure

Шлюз приложений — это балансировщик нагрузки уровня 7. Он отвечает за отработку отказов и эффективную маршрутизацию HTTP-запросов между разными серверами (облачными и локальными). Шлюз приложения имеет следующие функции доставки приложений: балансировка нагрузки HTTP, сродство сеансов на основе файлов cookie и безопасность уровня транспорта (TLS), ранее известная как Безопасный слой розеток (SSL), разгрузка, пользовательские зонды работоспособности и поддержка нескольких сайтов.

## <a name="prerequisite-install-the-azure-cli"></a>Предварительные требования. Установка Azure CLI

Для выполнения шагов в этой статье необходимо [установить Azure CLI](../xplat-cli-install.md) и [зарегистрироваться в Azure.](/cli/azure/authenticate-azure-cli) 

> [!NOTE]
> Если у вас нет учетной записи Azure, то вам потребуется получить ее. Зарегистрируйтесь, чтобы получить [бесплатную пробную версию](../active-directory/fundamentals/sign-up-organization.md).

## <a name="scenario"></a>Сценарий

В этом сценарии вы узнаете, как создать шлюз приложений с помощью портала Azure.

Вы узнаете:

* как создать средний шлюз приложений с двумя экземплярами;
* как создать виртуальную сеть ContosoVNET с зарезервированным блоком CIDR (10.0.0.0/16);
* как создать подсеть subnet01 с блоком CIDR (10.0.0.0/28).

> [!NOTE]
> Дополнительная настройка шлюза приложений, включая пользовательские пробы работоспособности, серверный пул адресов и дополнительные правила, осуществляется после настройки шлюза приложений, а не во время первоначального развертывания.

## <a name="before-you-begin"></a>Перед началом

Шлюзу приложений Azure требуется собственная подсеть. При создании виртуальной сети обязательно оставьте достаточно адресного пространства для нескольких подсетей. После развертывания шлюза приложений в подсети в нее можно добавлять только дополнительные шлюзы приложений.

## <a name="sign-in-to-azure"></a>Вход в Azure

Откройте **командный запрос Microsoft Azure**и вопийте.

```azurecli-interactive
az login
```

Введя предыдущий пример, вы получите код. Перейдите https://aka.ms/devicelogin в браузер, чтобы продолжить знак на процессе.

![Команда, выводящая имя пользователя устройства][1]

В браузере введите полученный код. Вы будете перенаправлены на страницу входа.

![Ввод кода в браузере][2]

Когда вы выполните вход, введя код, закройте браузер, чтобы продолжить работу.

![Вход выполнен][3]

## <a name="switch-to-resource-manager-mode"></a>Переключение в режим Resource Manager

```azurecli-interactive
azure config mode arm
```

## <a name="create-the-resource-group"></a>Создание группы ресурсов

Перед созданием шлюза приложений создается группа ресурсов, которая будет содержать шлюз приложений. Ниже показана команда для создания группы ресурсов.

```azurecli-interactive
azure group create \
--name ContosoRG \
--location eastus
```

## <a name="create-a-virtual-network"></a>Создание виртуальной сети

Сразу после создания группы ресурсов создается виртуальная сеть для шлюза приложений.  В примере ниже использовалось адресное пространство 10.0.0.0/16, определенное в предыдущих заметках о сценарии.

```azurecli-interactive
azure network vnet create \
--name ContosoVNET \
--address-prefixes 10.0.0.0/16 \
--resource-group ContosoRG \
--location eastus
```

## <a name="create-a-subnet"></a>Создание подсети

После создания виртуальной сети добавляется подсеть для шлюза приложений.  Если вы планируете использовать шлюз приложений с веб-приложением, размещенным в той же виртуальной сети, что и шлюз приложений, оставьте место для другой подсети.

```azurecli-interactive
azure network vnet subnet create \
--resource-group ContosoRG \
--name subnet01 \
--vnet-name ContosoVNET \
--address-prefix 10.0.0.0/28 
```

## <a name="create-the-application-gateway"></a>Создание шлюза приложений

После создания виртуальной сети и подсети можно считать, что все предварительные требования для шлюза приложений соблюдены. Кроме того, далее вам понадобятся ранее экспортированный PFX-сертификат и пароль для сертификата. IP-адреса, используемые для серверной части, — это IP-адреса для внутреннего сервера. Эти значения могут быть частными IP-адресами в виртуальной сети, общедоступными IP-адресами или полными доменными именами для внутренних серверов.

```azurecli-interactive
azure network application-gateway create \
--name AdatumAppGateway \
--location eastus \
--resource-group ContosoRG \
--vnet-name ContosoVNET \
--subnet-name subnet01 \
--servers 134.170.185.46,134.170.188.221,134.170.185.50 \
--capacity 2 \
--sku-tier Standard \
--routing-rule-type Basic \
--frontend-port 80 \
--http-settings-cookie-based-affinity Enabled \
--http-settings-port 80 \
--http-settings-protocol http \
--frontend-port http \
--sku-name Standard_Medium
```

> [!NOTE]
> Чтобы вывести список параметров, которые можно указать во время создания, выполните следующую команду: **azure network application-gateway create --help**.

В этом примере создается базовый шлюз приложений с параметрами по умолчанию для прослушивателя, серверного пула, протокола HTTP серверной части и правил. Вы сможете изменить эти параметры в соответствии с развертыванием после успешного завершения подготовки.
Если на предыдущем шаге вы уже определили для веб-приложения внутренний пул, то после создания шлюза запускается балансировка нагрузки.

## <a name="next-steps"></a>Следующие шаги

Узнайте, как создавать пользовательские пробы работоспособности, посетив страницу [Create a custom probe for Application Gateway by using the portal](application-gateway-create-probe-portal.md)

Узнайте, как настроить TLS Разгрузки и принять дорогостоящие TLS расшифровки с веб-серверов, посетив [Настройка TLS Разгрузки](application-gateway-ssl-arm.md)

<!--Image references-->

[scenario]: ./media/application-gateway-create-gateway-cli-nodejs/scenario.png
[1]: ./media/application-gateway-create-gateway-cli-nodejs/figure1.png
[2]: ./media/application-gateway-create-gateway-cli-nodejs/figure2.png
[3]: ./media/application-gateway-create-gateway-cli-nodejs/figure3.png
