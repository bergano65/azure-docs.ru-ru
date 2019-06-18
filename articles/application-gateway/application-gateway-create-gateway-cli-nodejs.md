---
title: 'Создание шлюза приложений Azure — Классическая модель: CLI Azure'
description: Создание Шлюза приложений с помощью классического Azure CLI в Resource Manager
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: conceptual
ms.date: 4/15/2019
ms.author: victorh
ms.openlocfilehash: 7107f45253c4f13b3378489726bf5034e104fa30
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "62095988"
---
# <a name="create-an-application-gateway-by-using-the-azure-cli"></a>Создание шлюза приложений с помощью интерфейса командной строки Azure

Шлюз приложений — это балансировщик нагрузки уровня 7. Он отвечает за отработку отказов и эффективную маршрутизацию HTTP-запросов между разными серверами (облачными и локальными). Шлюз приложений имеет такие функции доставки приложений: Балансировка нагрузки HTTP, определение сходства сеансов на основе файлов cookie и разгрузки Secure Sockets Layer (SSL), пользовательские пробы работоспособности и поддержку нескольких сайтов.

## <a name="prerequisite-install-the-azure-cli"></a>Предварительные требования: Установка Azure CLI

Чтобы выполнить действия в этой статье, вам потребуется [установите Azure CLI](../xplat-cli-install.md) и вам нужно [войдите в Azure](/cli/azure/authenticate-azure-cli). 

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

## <a name="before-you-begin"></a>Перед началом работы

Шлюзу приложений Azure требуется собственная подсеть. При создании виртуальной сети обязательно оставьте достаточно адресного пространства для нескольких подсетей. После развертывания шлюза приложений в подсети в нее можно добавлять только дополнительные шлюзы приложений.

## <a name="sign-in-to-azure"></a>Вход в Azure

Откройте **командную строку Microsoft Azure**и войдите в систему.

```azurecli-interactive
az login
```

Введя предыдущий пример, вы получите код. Перейдите к https://aka.ms/devicelogin в браузере, чтобы продолжить процесс входа.

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

## <a name="create-a-virtual-network"></a>Создать виртуальную сеть

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

После создания виртуальной сети и подсети можно считать, что все предварительные требования для шлюза приложений соблюдены. Кроме того ранее экспортированный PFX-сертификат и пароль для сертификата для необходимы следующие действия: IP-адреса серверной части — это IP-адреса внутреннего сервера. Эти значения могут быть частными IP-адресами в виртуальной сети, общедоступными IP-адресами или полными доменными именами для внутренних серверов.

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

## <a name="next-steps"></a>Дальнейшие действия

Узнайте, как создавать пользовательские пробы работоспособности, посетив страницу [Create a custom probe for Application Gateway by using the portal](application-gateway-create-probe-portal.md)

Узнайте, как настроить разгрузку SSL и убрать дорогостоящее шифрование SSL с веб-серверов, посетив страницу [Настройка шлюза приложений для разгрузки SSL с помощью диспетчера ресурсов Azure](application-gateway-ssl-arm.md)

<!--Image references-->

[scenario]: ./media/application-gateway-create-gateway-cli-nodejs/scenario.png
[1]: ./media/application-gateway-create-gateway-cli-nodejs/figure1.png
[2]: ./media/application-gateway-create-gateway-cli-nodejs/figure2.png
[3]: ./media/application-gateway-create-gateway-cli-nodejs/figure3.png
