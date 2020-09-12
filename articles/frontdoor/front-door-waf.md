---
title: Быстрое масштабирование и защита веб-приложения с помощью Azure Front Door и брандмауэра веб-приложения Azure (WAF) | Документация Майкрософт
description: Эта статья поможет вам понять, как использовать брандмауэр веб-приложения в службе "Передняя дверь" Azure.
services: frontdoor
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/06/2020
ms.author: duau
ms.openlocfilehash: a0252004b01e64b195b372d72682f6b777012258
ms.sourcegitcommit: c52e50ea04dfb8d4da0e18735477b80cafccc2cf
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/08/2020
ms.locfileid: "89535437"
---
# <a name="quickly-scale-and-protect-a-web-application-using-azure-front-door-and-azure-web-application-firewall-waf"></a>Быстрое масштабирование и защита веб-приложения с помощью службы Azure Front Door и брандмауэра веб-приложения Azure (WAF)

В последние недели многие веб-приложения столкнулись с быстрым ростом объема трафика из-за пандемии КОВИД-19. Кроме того, увеличился объем вредоносного трафика веб-приложений, в том числе трафика, связанного с DDoS-атаками. Чтобы эффективно обеспечить масштабирование с учетом возросшего объема трафика и защиту от атак, можно настроить для веб-приложения службу Azure Front Door с брандмауэром Azure WAF, которая гарантирует безопасность, эффективное кэширование и ускорение работы. В этой статье описывается, как можно быстро настроить Azure Front Door с Azure WAF для любых веб-приложений, запущенных в инфраструктуре Azure или за ее пределами. 

В этом руководстве для настройки WAF используется Azure CLI, однако эти действия также полностью поддерживаются на портале Azure, в Azure PowerShell, Azure ARM и API-интерфейсах Azure. 

## <a name="prerequisites"></a>Предварительные требования

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу. 

Инструкции в этом блоге предполагают использование интерфейса командной строки Azure (CLI). Просмотрите это руководство по [началу работы с Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest).

*Совет. Чтобы быстро и легко начать работать с Azure CLI, можно использовать [оболочку bash в Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/quickstart)*

Убедитесь, что в Azure CLI добавлено расширение front-door

```azurecli-interactive 
az extension add --name front-door
```

Примечание. Дополнительные сведения о командах, перечисленных ниже, см. в [справочнике по Azure CLI для работы со службой Front Door](https://docs.microsoft.com/cli/azure/ext/front-door/?view=azure-cli-latest).

## <a name="step-1-create-an-azure-front-door-afd-resource"></a>Шаг 1. Создание ресурса Azure Front Door (AFD)


```azurecli-interactive 
az network front-door create --backend-address <>  --accepted-protocols <> --name <> --resource-group <>
```

**--backend-address**: Параметр backend address содержит полное доменное имя приложения, для которого требуется настроить защиту. Например, myapplication.contoso.com

**--accepted-protocols**: Параметр accepted protocols определяет, какие протоколы должны поддерживаться в AFD для вашего веб-приложения. Например, --accepted-protocols Http Https.

**--name**: Укажите имя своего ресурса AFD

**--resource-group**: Группа ресурсов, в которую необходимо поместить ресурс AFD.  Дополнительные сведения о группах ресурсов см. в статье "Группы ресурсов" в Azure

В отклике, полученном после успешного выполнения этой команды, найдите ключ hostName и запишите его значение для использования в следующем шаге. Ключ hostName представляет собой имя DNS для созданного ресурса AFD

## <a name="step-2-create-an-azure-waf-profile-to-use-with-azure-front-door-resources"></a>Шаг 2. Создание профиля Azure WAF, который будет использоваться с ресурсами Azure Front Door

```azurecli-interactive 
az network front-door waf-policy create --name <>  --resource-group <>  --disabled false --mode Prevention
```

--name Укажите имя своей политики Azure WAF

--resource-group Группа ресурсов, в которую необходимо поместить ресурс WAF. 

Указанный выше код CLI создаст политику WAF, которая активирована и переведена в режим предотвращения. 

Примечание. Возможно, вам также потребуется создать политику WAF в режиме обнаружения и определить, как она будет обнаруживать и регистрировать вредоносные запросы (не блокируя их), прежде чем принять решение о переходе в режим защиты.

В отклике, полученном после успешного выполнения этой команды, найдите ключ ID и запишите его значение для использования в следующем шаге. Поле ID должно иметь следующий формат.

/subscriptions/**subscription id**/resourcegroups/**resource group name**/providers/Microsoft.Network/frontdoorwebapplicationfirewallpolicies/**WAF policy name**

## <a name="step-3-add-managed-rulesets-to-this-waf-policy"></a>Шаг 3. Добавление управляемых наборов правил в политику WAF

В политике WAF можно добавить управляемые наборы правил, созданные и управляемые корпорацией Майкрософт, которые обеспечивают защиту от различных классов угроз. В этом наборе показано добавление двух таких наборов правил: (1) набор правил по умолчанию, обеспечивающий защиту от самых распространенных веб-угроз, и (2) набор правил для защиты от ботов, обеспечивающий защиту от вредоносных ботов.

(1) Добавление набора правил по умолчанию

```azurecli-interactive 
az network front-door waf-policy managed-rules add --policy-name <> --resource-group <> --type DefaultRuleSet --version 1.0
```

(2) Добавление набора правил для диспетчера Bot

```azurecli-interactive 
az network front-door waf-policy managed-rules add --policy-name <> --resource-group <> --type Microsoft_BotManagerRuleSet --version 1.0
```

--policy-name Имя, заданное для ресурса Azure WAF

--resource-group Группа ресурсов, в которую помещен ресурс WAF.

## <a name="step-4-associate-the-waf-policy-with-the-afd-resource"></a>Шаг 4. Привязка политики WAF к ресурсу AFD

В этом шаге мы будем настраивать связь политики WAF, созданную с ресурсом AFD, который помещен перед веб-приложением.

```azurecli-interactive 
az network front-door update --name <> --resource-group <> --set frontendEndpoints[0].webApplicationFirewallPolicyLink='{"id":"<>"}'
```

--name Имя, указанное для ресурса AFD.

--resource-group Группа ресурсов, в которую помещен ресурс Azure Front Door.

--set Здесь обновляется атрибут WebApplicationFirewallPolicyLink свойства frontendEndpoint, связанного с ресурсом AFD с помощью созданной политики WAF. Идентификатор политики WAF указан в выходных данных, полученный в шаге 2 выше

Примечание. Приведенный выше пример применим к ситуациям, когда не используется личный домен.

Если вы не используете личные домены для доступа к веб-приложениям, можно пропустить шаг 5. В этом случае вы будете предоставлять конечным пользователям имя узла, полученное в шаге 1, для перехода к веб-приложению.

## <a name="step-5-configure-custom-domain-for-your-web-application"></a>Шаг 5. Настройка личного домена для веб-приложения

Изначально имя личного домена для веб-приложения (имя, которое используют заказчики для ссылки на приложение, например, www.contoso.com) указывало на расположение, где оно было запущено до появления AFD. После изменения архитектуры путем добавления AFD+WAF перед приложением запись DNS, соответствующая этому личному домену, должна теперь указывать на этот ресурс AFD. Для этого можно повторно сопоставить эту запись на DNS-сервере с именем узла AFD, записанным в шаге 1.

Конкретные действия по обновлению записей DNS будут зависеть от поставщика службы DNS, однако если вы используете Azure DNS для размещения DNS-имени, то обратитесь к документации по [обновлению записей DNS](https://docs.microsoft.com/azure/dns/dns-operations-recordsets-cli) и укажите на параметр hostName AFD. 

Здесь стоит обратить внимание на то, что если вам нужно, чтобы пользователи могли переходить на веб-сайт с помощью зоны вершине, например contoso.com, необходимо использовать Azure DNS и [тип записи псевдонима](https://docs.microsoft.com/azure/dns/dns-alias) для размещения DNS-имени. 

Кроме того, необходимо обновить конфигурацию AFD и [добавить этот личный домен](https://docs.microsoft.com/azure/frontdoor/front-door-custom-domain), чтобы AFD приняла это сопоставление.

Если вы используете личный домен для доступа к веб-приложению и хотите включить поддержку протокола HTTPS, потребуется [настроить в AFD сертификаты личного домена](https://docs.microsoft.com/azure/frontdoor/front-door-custom-domain-https). 

## <a name="step-6-lock-down-your-web-application"></a>Шаг 6. Блокировка веб-приложения

Рекомендуется также настроить веб-приложение таким образом, чтобы оно могло обмениваться данными только с пограничными зонами AFD. Благодаря этому никто не сможет обойти защиту AFD и получить доступ напрямую к вашим приложениям. Для получения сведения по настройке такой блокировки перейдите в раздел [часто задаваемых вопросов об AFD](https://docs.microsoft.com/azure/frontdoor/front-door-faq) и ознакомьтесь с инструкциями по блокировке серверной части и разрешению доступа только для AFD.
