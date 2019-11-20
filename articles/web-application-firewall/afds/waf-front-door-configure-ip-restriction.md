---
title: Настройка правила WAF ограничений IP-адресов для службы "Передняя дверь" Azure
description: Узнайте, как настроить правило брандмауэра веб-приложения, чтобы ограничить IP-адреса для существующей конечной точки службы передней дверцы Azure.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.topic: article
ms.date: 08/21/2019
ms.author: victorh
ms.reviewer: tyao
ms.openlocfilehash: 2917b2f04e7c5a4896c52861ab7eab4e0eb00b5d
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/19/2019
ms.locfileid: "74186687"
---
# <a name="configure-an-ip-restriction-rule-with-a-web-application-firewall-for-azure-front-door-service"></a>Настройка правила ограничения IP-адресов с брандмауэром веб-приложения для службы "Передняя дверь" Azure
В этой статье показано, как настроить правила ограничения IP-адресов в брандмауэре веб-приложения (WAF) для службы "Передняя дверь" Azure с помощью Azure CLI, Azure PowerShell или шаблона Azure Resource Manager.

Правило управления доступом на основе IP-адресов — это настраиваемое правило WAF, которое позволяет управлять доступом к веб-приложениям. Для этого нужно указать список IP-адресов или диапазонов IP-адресов в формате междоменной маршрутизации (CIDR) без классов.

По умолчанию веб-приложение доступно через Интернет. Если требуется ограничить доступ клиентов из списка известных IP-адресов или диапазонов IP-адресов, можно создать правило сопоставления IP-адресов, содержащее список IP-адреса в качестве совпадающих значений и задать оператору значение "не" (отрицание имеет значение true) и действие для **блокировки**. После применения правила ограничения IP-адресов запросы, поступающие за пределы этого списка разрешенных, получают ответ 403.  

## <a name="configure-a-waf-policy-with-the-azure-cli"></a>Настройка политики WAF с помощью Azure CLI

### <a name="prerequisites"></a>предварительным требованиям
Прежде чем приступить к настройке политики ограничения IP-адресов, настройте среду CLI и создайте профиль службы передней дверцы Azure.

#### <a name="set-up-the-azure-cli-environment"></a>Настройка среды Azure CLI
1. Установите [Azure CLI](/cli/azure/install-azure-cli)или используйте Azure Cloud Shell. Azure Cloud Shell — это бесплатная оболочка Bash, которую можно запускать непосредственно на портале Azure. Она включает предварительно установленный интерфейс Azure CLI и настроена для использования с вашей учетной записью. Нажмите кнопку **попробовать** в следующих командах интерфейса командной строки, а затем войдите в свою учетную запись Azure в открывшемся сеансе Cloud Shell. После запуска сеанса введите `az extension add --name front-door`, чтобы добавить расширение службы "Передняя дверца Azure".
 2. Если вы используете интерфейс командной строки локально в bash, войдите в Azure с помощью `az login`.

#### <a name="create-an-azure-front-door-service-profile"></a>Создание профиля службы "Передняя дверца Azure"
Создайте профиль службы "Передняя дверца Azure", следуя инструкциям, описанным в разделе [Краткое руководство. Создание передней дверцы для глобального веб-приложения высокой доступности](../../frontdoor/quickstart-create-front-door.md).

### <a name="create-a-waf-policy"></a>создание политики WAF;

Создайте политику WAF с помощью команды [AZ Network Front-дверь WAF-Policy Create](/cli/azure/ext/front-door/network/front-door/waf-policy?view=azure-cli-latest#ext-front-door-az-network-front-door-waf-policy-create) . В следующем примере замените имя политики *ипалловполициексамплекли* на уникальное имя политики.

```azurecli-interactive 
az network front-door waf-policy create \
  --resource-group <resource-group-name> \
  --subscription <subscription ID> \
  --name IPAllowPolicyExampleCLI
  ```
### <a name="add-a-custom-ip-access-control-rule"></a>Добавление настраиваемого правила контроля доступа IP-адресов

Чтобы добавить настраиваемое правило контроля доступа IP-адресов для только что созданной политики WAF, используйте команду [AZ Network Front-дверь WAF-Policy Custom-Rule Create](/cli/azure/ext/front-door/network/front-door/waf-policy/rule?view=azure-cli-latest#ext-front-door-az-network-front-door-waf-policy-rule-create) .

В следующих примерах:
-  Замените *ипалловполициексамплекли* своей уникальной политикой, созданной ранее.
-  Замените *IP-Address-Range-1*, *IP-Address-Range-2* своим собственным диапазоном.

Сначала создайте правило IP-разрешения для политики, созданной на предыдущем шаге. Примечание. параметр **отсрочки** необходим, так как правило должно иметь условие соответствия, которое будет добавлено на следующем шаге.

```azurecli
az network front-door waf-policy rule create \
  --name IPAllowListRule \
  --priority 1 \
  --rule-type MatchRule \
  --action Block \
  --resource-group <resource-group-name> \
  --policy-name IPAllowPolicyExampleCLI --defer
```
Затем добавьте условие соответствия в правило:

```azurecli
az network front-door waf-policy rule match-condition add\
--match-variable RemoteAddr \
--operator IPMatch
--values "ip-address-range-1" "ip-address-range-2"
--negate true\
--name IPAllowListRule\
  --resource-group <resource-group-name> \
  --policy-name IPAllowPolicyExampleCLI 
  ```
                                                   
### <a name="find-the-id-of-a-waf-policy"></a>Поиск идентификатора политики WAF 
Найдите идентификатор политики WAF с помощью команды [AZ Network Front-двери WAF-Policy показывать](/cli/azure/ext/front-door/network/front-door/waf-policy?view=azure-cli-latest#ext-front-door-az-network-front-door-waf-policy-show) . Замените *ипалловполициексамплекли* в следующем примере своей уникальной политикой, созданной ранее.

   ```azurecli
   az network front-door  waf-policy show \
     --resource-group <resource-group-name> \
     --name IPAllowPolicyExampleCLI
   ```

### <a name="link-a-waf-policy-to-an-azure-front-door-service-front-end-host"></a>Связывание политики WAF с интерфейсным узлом службы передней дверцы Azure

Задайте идентификатор политики в качестве идентификатора *вебаппликатионфиреваллполицилинк* для службы "Передняя дверца Azure" с помощью команды [AZ Network Front-двери Update](/cli/azure/ext/front-door/network/front-door?view=azure-cli-latest#ext-front-door-az-network-front-door-update) . Замените *ипалловполициексамплекли* своей уникальной политикой, созданной ранее.

   ```azurecli
   az network front-door update \
     --set FrontendEndpoints[0].WebApplicationFirewallPolicyLink.id=/subscriptions/<subscription ID>/resourcegroups/<resource- name>/providers/Microsoft.Network/frontdoorwebapplicationfirewallpolicies/IPAllowPolicyExampleCLI \
     --name <frontdoor-name>
     --resource-group <resource-group-name>
   ```
В этом примере политика WAF применяется к **фронтендендпоинтс [0]** . Вы можете связать политику WAF с любыми внешними интерфейсами.
> [!Note]
> Необходимо задать свойство **вебаппликатионфиреваллполицилинк** только один раз, чтобы связать политику WAF с внешним интерфейсом службы передней дверцы Azure. Последующие обновления политики применяются к внешнему интерфейсу автоматически.

## <a name="configure-a-waf-policy-with-azure-powershell"></a>Настройка политики WAF с Azure PowerShell

### <a name="prerequisites"></a>предварительным требованиям
Прежде чем приступить к настройке политики ограничения IP-адресов, настройте среду PowerShell и создайте профиль службы передней дверцы Azure.

#### <a name="set-up-your-powershell-environment"></a>Настройка среды PowerShell
Azure PowerShell предоставляет набор командлетов, использующих модель [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) для управления ресурсами Azure.

Вы можете установить [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) на локальном компьютере и использовать его в любом сеансе PowerShell. Следуйте инструкциям на странице, чтобы войти в PowerShell с помощью учетных данных Azure, а затем установите модуль AZ.

1. Подключитесь к Azure с помощью следующей команды, а затем используйте интерактивное диалоговое окно для входа.
    ```
    Connect-AzAccount
    ```
 2. Перед установкой модуля службы "Передняя дверца Azure" убедитесь, что установлена текущая версия модуля PowerShellGet. Выполните следующую команду, а затем снова откройте PowerShell.

    ```
    Install-Module PowerShellGet -Force -AllowClobber
    ``` 

3. Установите модуль AZ. FrontDoor с помощью следующей команды. 
    
    ```
    Install-Module -Name Az.FrontDoor
    ```
### <a name="create-an-azure-front-door-service-profile"></a>Создание профиля службы "Передняя дверца Azure"
Создайте профиль службы "Передняя дверца Azure", следуя инструкциям, описанным в разделе [Краткое руководство. Создание передней дверцы для глобального веб-приложения высокой доступности](../../frontdoor/quickstart-create-front-door.md).

### <a name="define-an-ip-match-condition"></a>Определение условия соответствия IP-адресов
Используйте команду [New-азфронтдурвафматчкондитионобжект](/powershell/module/az.frontdoor/new-azfrontdoorwafmatchconditionobject) , чтобы определить условие соответствия IP-адресов.
В следующем примере замените *IP-Address-Range-1*, *IP-Address-Range-2* своим собственным диапазоном.    
```powershell
$IPMatchCondition = New-AzFrontDoorWafMatchConditionObject `
-MatchVariable  RemoteAddr `
-OperatorProperty IPMatch `
-MatchValue "ip-address-range-1", "ip-address-range-2"
-NegateCondition 1
```
     
### <a name="create-a-custom-ip-allow-rule"></a>Создание настраиваемого правила IP-адресов

Используйте команду [New-азфронтдуркустомрулеобжект](/powershell/module/Az.FrontDoor/New-azfrontdoorwafcustomruleobject) , чтобы определить действие и задать приоритет. В следующем примере запросы, не являющиеся IP-адресами клиентов, которые соответствуют списку, будут заблокированы.

```powershell
$IPAllowRule = New-AzFrontDoorCustomRuleObject `
-Name "IPAllowRule" `
-RuleType MatchRule `
-MatchCondition $IPMatchCondition `
-Action Block -Priority 1
```

### <a name="configure-a-waf-policy"></a>Настройка политики WAF
Найдите имя группы ресурсов, содержащей профиль службы "Передняя дверца Azure", с помощью `Get-AzResourceGroup`. Затем настройте политику WAF с использованием правила IP-адресов с помощью [New-азфронтдурвафполици](/powershell/module/az.frontdoor/new-azfrontdoorwafpolicy).

```powershell
  $IPAllowPolicyExamplePS = New-AzFrontDoorWafPolicy `
    -Name "IPRestrictionExamplePS" `
    -resourceGroupName <resource-group-name> `
    -Customrule $IPAllowRule`
    -Mode Prevention `
    -EnabledState Enabled
   ```

### <a name="link-a-waf-policy-to-an-azure-front-door-service-front-end-host"></a>Связывание политики WAF с интерфейсным узлом службы передней дверцы Azure

Свяжите объект политики WAF с существующим внешним узлом и обновите свойства службы "Передняя дверца Azure". Сначала получите объект службы "Передняя дверца Azure" с помощью команды [Get-азфронтдур](/powershell/module/Az.FrontDoor/Get-AzFrontDoor). Затем задайте для свойства **ВЕБАППЛИКАТИОНФИРЕВАЛЛПОЛИЦИЛИНК** идентификатор ресурса *$IPAllowPolicyExamplePS*, созданный на предыдущем шаге, с помощью команды [Set-азфронтдур](/powershell/module/Az.FrontDoor/Set-AzFrontDoor) .

```powershell
  $FrontDoorObjectExample = Get-AzFrontDoor `
    -ResourceGroupName <resource-group-name> `
    -Name $frontDoorName
  $FrontDoorObjectExample[0].FrontendEndpoints[0].WebApplicationFirewallPolicyLink = $IPBlockPolicy.Id
  Set-AzFrontDoor -InputObject $FrontDoorObjectExample[0]
```

> [!NOTE]
> В этом примере политика WAF применяется к **фронтендендпоинтс [0]** . Вы можете связать политику WAF с любыми внешними интерфейсами. Необходимо задать свойство **вебаппликатионфиреваллполицилинк** только один раз, чтобы связать политику WAF с внешним интерфейсом службы передней дверцы Azure. Последующие обновления политики применяются к внешнему интерфейсу автоматически.


## <a name="configure-a-waf-policy-with-a-resource-manager-template"></a>Настройка политики WAF с помощью шаблона диспетчер ресурсов
Чтобы просмотреть шаблон, который создает политику службы "Передняя дверца Azure" и политику WAF с настраиваемыми правилами ограничения IP-адресов, перейдите на сайт [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-waf-clientip).


## <a name="next-steps"></a>Дополнительная информация

- Узнайте, как [создать профиль службы "Передняя дверца Azure](../../frontdoor/quickstart-create-front-door.md)".
