---
title: Настройка правило ограничения IP-адрес с правилом брандмауэра веб приложения для службы Azure двери
description: Узнайте, как настроить правила брандмауэра веб приложения для ограничения IP-адреса для существующей конечной точки службы Azure двери.
services: frontdoor
documentationcenter: ''
author: KumudD
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/31/2019
ms.author: kumud;tyao
ms.openlocfilehash: 73ef16aeb9a6014e98c0d40314bc174c6b5bf307
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66808358"
---
# <a name="configure-an-ip-restriction-rule-with-a-web-application-firewall-for-azure-front-door-service"></a>Настройка правила ограничения IP-адрес с брандмауэром веб-приложения для службы Azure двери
В этой статье показано, как настроить правила ограничения IP-адресов в брандмауэр веб-приложения (WAF) для службы Azure двери с помощью Azure CLI, Azure PowerShell или шаблона Azure Resource Manager.

Правила управления доступом на основе адреса IP-адрес — это настраиваемое правило WAF, которая позволяет управлять доступом к веб-приложений. Это делается путем указания списка IP-адреса или диапазоны IP-адресов в формате бесклассовой междоменной маршрутизации (CIDR).

По умолчанию вашего веб-приложение доступно из Интернета. Если вы хотите ограничить доступ для клиентов из списка известных IP-адреса или диапазоны IP-адресов, необходимо создать два правила сопоставления IP-адрес. Первое соответствующее правило IP-адрес содержит список IP-адресов как совпадающие значения и делает действием **Разрешить**. Второй, с низким приоритетом, блокирует все другие IP-адреса с помощью **все** оператор и для параметра действие **блок**. После применения правила ограничения IP-адресов, запросы, отправленные из адреса, которые не входят в список разрешенных ответ 403 запрещено.  

## <a name="configure-a-waf-policy-with-the-azure-cli"></a>Настройка политики WAF с помощью Azure CLI

### <a name="prerequisites"></a>Технические условия
Прежде чем приступать к настройке IP-адрес политики ограниченного использования программ, настройте среду интерфейса командной строки и создать профиль службы Azure двери.

#### <a name="set-up-the-azure-cli-environment"></a>Настройка среды интерфейса командной строки Azure
1. Установка [Azure CLI](/cli/azure/install-azure-cli), или использовать Azure Cloud Shell. Azure Cloud Shell — это бесплатная оболочка Bash, которую можно запускать непосредственно на портале Azure. Она включает предварительно установленный интерфейс Azure CLI и настроена для использования с вашей учетной записью. Выберите **попробовать** кнопки в командах интерфейса командной строки, выполните, а затем войдите в свою учетную запись Azure в открывшемся сеансе Cloud Shell. После начала сеанса, введите `az extension add --name front-door` для добавления расширения двери службы Azure.
 2. Если вы используете CLI локально в Bash, войдите в Azure с помощью `az login`.

#### <a name="create-an-azure-front-door-service-profile"></a>Создание профиля двери службы Azure
Создание профиля двери службы Azure, следуя инструкциям, описанным в [краткое руководство: Создание передовой высокой доступности глобального веб-приложения](quickstart-create-front-door.md).

### <a name="create-a-waf-policy"></a>Создание политики WAF

Создание политики WAF с помощью [создать az сети waf-policy](/cli/azure/ext/front-door/network/waf-policy?view=azure-cli-latest#ext-front-door-az-network-waf-policy-create) команды. В примере, что следующим, замените имя политики *IPAllowPolicyExampleCLI* с уникальное имя политики.

```azurecli-interactive 
az network waf-policy create \
  --resource-group <resource-group-name> \
  --subscription <subscription ID> \
  --name IPAllowPolicyExampleCLI
  ```
### <a name="add-a-custom-ip-access-control-rule"></a>Добавить настраиваемое правило управления доступом IP-адрес

Используйте [Azure network waf-policy custom-rule create](/cli/azure/ext/front-door/network/waf-policy/custom-rule?view=azure-cli-latest#ext-front-door-az-network-waf-policy-custom-rule-create) команду, чтобы добавить пользовательский IP-адрес правила управления доступом для политики WAF вы только что создали.

В следующих примерах:
-  Замените *IPAllowPolicyExampleCLI* с вашей уникальную политику, созданную ранее.
-  Замените *ip адрес диапазона-1*, *ip адрес диапазона-2* с собственного диапазона.

Во-первых, создайте IP-адрес разрешающее правило для указанных адресов.

```azurecli
az network waf-policy custom-rule create \
  --name IPAllowListRule \
  --priority 1 \
  --rule-type MatchRule \
  --match-condition RemoteAddr IPMatch ("<ip-address-range-1>","<ip-address-range-2>") \
  --action Allow \
  --resource-group <resource-group-name> \
  --policy-name IPAllowPolicyExampleCLI
```
Создайте **блокировать все** правило с более низким приоритетом по сравнению с предыдущим **Разрешить** правило. Еще раз замените *IPAllowPolicyExampleCLI* в следующем примере с вашей уникальную политику, созданную ранее.

```azurecli
az network waf-policy custom-rule create \
  --name IPDenyAllRule\
  --priority 2 \
  --rule-type MatchRule \
  --match-condition RemoteAddr Any
  --action Block \
  --resource-group <resource-group-name> \
  --policy-name IPAllowPolicyExampleCLI
```
    
### <a name="find-the-id-of-a-waf-policy"></a>Найдите идентификатор политики WAF 
Найти идентификатор политику WAF с помощью [az сети waf-policy show](/cli/azure/ext/front-door/network/waf-policy?view=azure-cli-latest#ext-front-door-az-network-waf-policy-show) команды. Замените *IPAllowPolicyExampleCLI* в следующем примере с вашей уникальную политику, созданную ранее.

   ```azurecli
   az network waf-policy show \
     --resource-group <resource-group-name> \
     --name IPAllowPolicyExampleCLI
   ```

### <a name="link-a-waf-policy-to-an-azure-front-door-service-front-end-host"></a>Связать политику WAF для Azure двери переднего плана размещения службы

Укажите службу двери Azure *WebApplicationFirewallPolicyLink* идентификатор идентификатор политики с помощью [az сети внутренними обновления](/cli/azure/ext/front-door/network/front-door?view=azure-cli-latest#ext-front-door-az-network-front-door-update) команды. Замените *IPAllowPolicyExampleCLI* с вашей уникальную политику, созданную ранее.

   ```azurecli
   az network front-door update \
     --set FrontendEndpoints[0].WebApplicationFirewallPolicyLink.id=/subscriptions/<subscription ID>/resourcegroups/<resource- name>/providers/Microsoft.Network/frontdoorwebapplicationfirewallpolicies/IPAllowPolicyExampleCLI \
     --name <frontdoor-name>
     --resource-group <resource-group-name>
   ```
В этом примере назначается политика WAF **FrontendEndpoints [0]** . Политика WAF можно связать любой из вашей внешних интерфейсов.
> [!Note]
> Необходимо задать **WebApplicationFirewallPolicyLink** свойство только один раз, чтобы связать политику WAF Azure двери внешнего интерфейса службы. Последующие политики обновления автоматически применяются к переднего плана.

## <a name="configure-a-waf-policy-with-azure-powershell"></a>Настройка политики WAF с помощью Azure PowerShell

### <a name="prerequisites"></a>Технические условия
Прежде чем приступать к настройке IP-адрес политики ограниченного использования программ, настройте среду PowerShell и создать профиль службы Azure двери.

#### <a name="set-up-your-powershell-environment"></a>Настройка среды PowerShell
Azure PowerShell предоставляет набор командлетов, используйте [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) модели для управления ресурсами Azure.

Вы можете установить [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) на локальном компьютере и использовать его в любом сеансе PowerShell. Следуйте инструкциям на странице для входа в PowerShell, используя свои учетные данные Azure, а затем установите модуль Az.

1. Подключиться к Azure с помощью следующей команды и затем использовать интерактивное диалоговое окно для входа.
    ```
    Connect-AzAccount
    ```
 2. Перед установкой модуль двери службы Azure, убедитесь, что у вас есть текущая версия установить модуль PowerShellGet. Выполните следующую команду и затем снова откройте PowerShell.

    ```
    Install-Module PowerShellGet -Force -AllowClobber
    ``` 

3. Установите модуль Az.FrontDoor, используя следующую команду. 
    
    ```
    Install-Module -Name Az.FrontDoor
    ```
### <a name="create-an-azure-front-door-service-profile"></a>Создание профиля двери службы Azure
Создание профиля двери службы Azure, следуя инструкциям, описанным в [краткое руководство: Создание передовой высокой доступности глобального веб-приложения](quickstart-create-front-door.md).

### <a name="define-an-ip-match-condition"></a>Определить условие соответствия IP-адрес
Используйте [New AzFrontDoorWafMatchConditionObject](/powershell/module/az.frontdoor/new-azfrontdoorwafmatchconditionobject) команду, чтобы определить условие соответствия IP-адрес.
В следующем примере замените *ip адрес диапазона-1*, *ip адрес диапазона-2* с собственного диапазона.    
```powershell
$IPMatchCondition = New-AzFrontDoorWafMatchConditionObject `
-MatchVariable  RemoteAddr `
-OperatorProperty IPMatch `
-MatchValue "ip-address-range-1", "ip-address-range-2"
```
Создание IP-адрес *все условие соответствия* правило с помощью следующей команды:
```powershell
$IPMatchALlCondition = New-AzFrontDoorWafMatchConditionObject `
-MatchVariable  RemoteAddr `
-OperatorProperty Any        
  ```
    
### <a name="create-a-custom-ip-allow-rule"></a>Создание пользовательского правила разрешенных IP-адресов

Используйте [New AzFrontDoorCustomRuleObject](/powershell/module/Az.FrontDoor/New-azfrontdoorwafcustomruleobject) команду, чтобы определить действие и установить приоритет. В следующем примере будут разрешены запросы от клиентских IP-адресов, соответствующих списку.

```powershell
$IPAllowRule = New-AzFrontDoorCustomRuleObject `
-Name "IPAllowRule" `
-RuleType MatchRule `
-MatchCondition $IPMatchCondition `
-Action Allow -Priority 1
```
Создание **блокировать все** правило с более низким приоритетом, чем предыдущий IP-адрес **Разрешить** правило.
```powershell
$IPBlockAll = New-AzFrontDoorCustomRuleObject `
-Name "IPDenyAll" `
-RuleType MatchRule `
-MatchCondition $IPMatchALlCondition `
-Action Block `
-Priority 2
```

### <a name="configure-a-waf-policy"></a>Настройка политики WAF
Найдите имя группы ресурсов, которая содержит профиль двери службы Azure с помощью `Get-AzResourceGroup`. Настройте политику WAF с IP-адресом **блокировать все** правило с помощью [New AzFrontDoorWafPolicy](/powershell/module/az.frontdoor/new-azfrontdoorwafpolicy).

```powershell
  $IPAllowPolicyExamplePS = New-AzFrontDoorWafPolicy `
    -Name "IPRestrictionExamplePS" `
    -resourceGroupName <resource-group-name> `
    -Customrule $IPAllowRule $IPBlockAll `
    -Mode Prevention `
    -EnabledState Enabled
   ```

### <a name="link-a-waf-policy-to-an-azure-front-door-service-front-end-host"></a>Связать политику WAF для Azure двери переднего плана размещения службы

Свяжите объект политики WAF существующего интерфейса узла и обновления двери свойства службы Azure. Сначала получите объект двери службы Azure с помощью [Get-AzFrontDoor](/powershell/module/Az.FrontDoor/Get-AzFrontDoor). Затем задайте **WebApplicationFirewallPolicyLink** присваивается идентификатор ресурса *$IPAllowPolicyExamplePS*, созданный на предыдущем шаге, с помощью [Set-AzFrontDoor](/powershell/module/Az.FrontDoor/Set-AzFrontDoor)команды.

```powershell
  $FrontDoorObjectExample = Get-AzFrontDoor `
    -ResourceGroupName <resource-group-name> `
    -Name $frontDoorName
  $FrontDoorObjectExample[0].FrontendEndpoints[0].WebApplicationFirewallPolicyLink = $IPBlockPolicy.Id
  Set-AzFrontDoor -InputObject $FrontDoorObjectExample[0]
```

> [!NOTE]
> В этом примере назначается политика WAF **FrontendEndpoints [0]** . Политику WAF можно привязать к любому из вашей внешних интерфейсов. Необходимо задать **WebApplicationFirewallPolicyLink** свойство только один раз, чтобы связать политику WAF Azure двери внешнего интерфейса службы. Последующие политики обновления автоматически применяются к переднего плана.


## <a name="configure-a-waf-policy-with-a-resource-manager-template"></a>Настройка политики WAF с помощью шаблона Resource Manager
Чтобы просмотреть шаблон, который создает политику службы Azure двери и политику WAF с помощью настраиваемых правил ограничения IP-адресов, перейдите к [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-waf-clientip).


## <a name="next-steps"></a>Дальнейшие действия

- Узнайте, как [создать профиль службы Azure двери](quickstart-create-front-door.md).
