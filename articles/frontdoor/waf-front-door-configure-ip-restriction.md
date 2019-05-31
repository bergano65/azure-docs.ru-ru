---
title: Настройка правила ограничения IP-адрес с правилом брандмауэра веб-приложения для входной двери Azure
description: Узнайте, как настроить правило WAF IP-адресов ограниченного использования программ для существующей конечной точки двери.
services: frontdoor
documentationcenter: ''
author: KumudD
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/21/2019
ms.author: kumud;tyao
ms.openlocfilehash: dae2bb8ece9ef56c0999e0f89abbf6f8d8e950e2
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/27/2019
ms.locfileid: "66242930"
---
# <a name="configure-an-ip-restriction-rule-with-web-application-firewall-for-azure-front-door-preview"></a>Настройка правила ограничения IP-адрес с помощью брандмауэра веб-приложения для Azure двери (Предварительная версия)
 В этой статье показано, как настроить правила ограничения IP-адресов в брандмауэр Azure веб-приложения (WAF) для входной двери с помощью шаблона Azure CLI, Azure PowerShell или Azure Resource Manager.

Правила управления доступом на основе адреса IP-адрес — это настраиваемое правило WAF, которая позволяет управлять доступом к веб-приложений, указав список IP-адреса или диапазоны IP-адресов в форме бесклассовой междоменной маршрутизации (CIDR).

По умолчанию вашего веб-приложение доступно из Интернета. Если вы хотите ограничить доступ к веб-приложений только для клиентов из списка известных IP-адреса или диапазоны IP-адресов, необходимо создать два правила сопоставления IP-адрес. Первое соответствующее правило IP-адрес содержит список IP-адресов как совпадающих значений и выберите действие «Разрешить». Второй с низким приоритетом, — блокировать все IP-адреса с помощью «All» оператора и параметра action задано значение «BLOCK». После применения правила ограничения IP-адресов любых запросов, поступивших с указанных адресов не входят в список разрешенных получает ответом 403 (запрещено).  

> [!IMPORTANT]
> Функции ограничения IP-адрес WAF для входной двери Azure в настоящее время находится в общедоступной предварительной версии.
> Эта предварительная версия предоставляется без соглашения об уровне обслуживания и не рекомендована для использования рабочей среде. Некоторые функции могут не поддерживаться или их возможности могут быть ограничены. Дополнительные сведения см. в статье [Дополнительные условия использования предварительных выпусков Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="configure-waf-policy-with-azure-cli"></a>Настройка политики WAF с помощью Azure CLI

### <a name="prerequisites"></a>Технические условия
Прежде чем приступать к настройке IP-адрес политики ограниченного использования программ, настройте среду интерфейса командной строки и создание передовой профиля.

#### <a name="set-up-azure-cli-environment"></a>Настройка среды интерфейса командной строки Azure
1. Установка [Azure CLI](/cli/azure/install-azure-cli), или использовать Azure Cloud Shell. Azure Cloud Shell — это бесплатная оболочка Bash, которую можно запускать непосредственно на портале Azure. У него есть CLI Azure предварительно установлен и настроен для использования с вашей учетной записью. Выберите **попробовать** ниже команды элементов управления button в интерфейсе командной строки. Выбрав **попробовать** вызывает Cloud Shell, который вы можете войти в учетную запись Azure с помощью. После запуска сеанса cloud shell введите `az extension add --name front-door` для добавления расширения внутренними.
 2. При использовании интерфейса командной строки локально, в Bash, войдите Azure с помощью `az login`.

#### <a name="create-front-door-profile"></a>Создание профиля передовой линии
Создайте профиль Front Door, следуя инструкциям, описанным в статье [Краткое руководство. Создание профиля передовой линии](quickstart-create-front-door.md)

### <a name="create-a-waf-policy"></a>Создание политики WAF

Создайте политику WAF с [создать az сети waf-policy](/cli/azure/ext/front-door/network/waf-policy?view=azure-cli-latest#ext-front-door-az-network-waf-policy-create) команды. В следующем примере замените имя политики *IPAllowPolicyExampleCLI* с уникальное имя политики.

```azurecli-interactive 
az network waf-policy create \
  --resource-group <resource-group-name> \
  --subscription <subscription ID> \
  --name IPAllowPolicyExampleCLI
  ```
### <a name="add-custom-ip-access-control-rule"></a>Добавить пользовательские правила управления доступом IP-адрес

Добавить настраиваемое правило управления доступом IP-адрес для WAF политику, созданную на предыдущем шаге, с [Azure network waf-policy custom-rule create](/cli/azure/ext/front-door/network/waf-policy/custom-rule?view=azure-cli-latest#ext-front-door-az-network-waf-policy-custom-rule-create) команды. 

В следующем примере:
-  Замените *IPAllowPolicyExampleCLI* с вашей уникальную политику, созданную ранее.
-  Замените *ip адрес диапазона-1*, *ip адрес диапазона-2* с собственного диапазона.

Во-первых, создайте IP-адрес разрешающее правило для указанных адресов.

```azurecli
az network waf-policy custom-rule create \
  --name IPAllowListRule \
  --priority 1 \
  --rule-type MatchRule \
  --match-condition RemoteAddr IPMatch ["<ip-address-range-1>","<ip-address-range-2>"] \
  --action Allow \
  --resource-group <resource-group-name> \
  --policy-name IPAllowPolicyExampleCLI
```
Создайте блок все правила IP-адресов с низким приоритетом, чем предыдущий IP-адрес правила. Замените *IPAllowPolicyExampleCLI* с вашей уникальную политику, созданную ранее.

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

### <a name="find-waf-policy-id"></a>Найдите идентификатор политики WAF
Найдите идентификатор политики WAF с [az сети waf-policy show](/cli/azure/ext/front-door/network/waf-policy?view=azure-cli-latest#ext-front-door-az-network-waf-policy-show) команды. Замените *IPAllowPolicyExampleCLI* с вашей уникальную политику, созданную ранее.

   ```azurecli
   az network waf-policy show \
     --resource-group <resource-group-name> \
     --name IPAllowPolicyExampleCLI
   ```

### <a name="link-waf-policy-to-a-front-door-front-end-host"></a>Ссылка WAF политики к узлу двери переднего плана

Задайте двери *WebApplicationFirewallPolicyLink* идентификатор идентификатор политики с [az сети внутренними обновления](/cli/azure/ext/front-door/network/front-door?view=azure-cli-latest#ext-front-door-az-network-front-door-update) команды. Замените *IPAllowPolicyExampleCLI* с вашей уникальную политику, созданную ранее.

   ```azurecli
   az network front-door update \
     --set FrontendEndpoints[0].WebApplicationFirewallPolicyLink.id=/subscriptions/<subscription ID>/resourcegroups/<resource- name>/providers/Microsoft.Network/frontdoorwebapplicationfirewallpolicies/IPAllowPolicyExampleCLI \
     --name <frontdoor-name>
     --resource-group <resource-group-name>
   ```
В этом примере WAF политики применяется к FrontendEndpoints [0]. Политика WAF можно привязать к любому из внешних интерфейсов.
> [!Note]
> Необходимо задать **WebApplicationFirewallPolicyLink** свойство один раз, чтобы связать политику WAF двери переднего плана. Последующие политики обновления автоматически применяются к внешней сети.

## <a name="configure-waf-policy-with-azure-powershell"></a>Настройка политики WAF с помощью Azure PowerShell

### <a name="prerequisites"></a>Технические условия
Прежде чем приступать к настройке IP-адрес политики ограниченного использования программ, настройте среду PowerShell и создание передовой профиля.

#### <a name="set-up-your-powershell-environment"></a>Настройка среды PowerShell
В Azure PowerShell доступен набор командлетов, которые используют модель [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) для управления ресурсами Azure. 

Вы можете установить [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) на локальном компьютере и использовать его в любом сеансе PowerShell. Следуйте инструкциям на странице, чтобы войти с использованием учетных данных Azure и установите модуль Az PowerShell.

##### <a name="connect-to-azure-with-an-interactive-dialog-for-sign-in"></a>Подключение к Azure с помощью интерактивное диалоговое окно для входа в систему
```
Connect-AzAccount

```
Перед установкой модуля двери, убедитесь, что у вас есть текущая версия установлена PowerShellGet. Выполните команду ниже и снова откройте PowerShell.

```
Install-Module PowerShellGet -Force -AllowClobber
``` 

##### <a name="install-azfrontdoor-module"></a>Установка модуля Az.FrontDoor 

```
Install-Module -Name Az.FrontDoor
```
### <a name="create-a-front-door-profile"></a>Создание профиля Front Door
Создайте профиль Front Door, следуя инструкциям, описанным в статье [Краткое руководство. Создание профиля передовой линии](quickstart-create-front-door.md)

### <a name="define-ip-match-condition"></a>Определить условие соответствия IP-адрес
Используйте [New AzFrontDoorWafMatchConditionObject](/powershell/module/az.frontdoor/new-azfrontdoorwafmatchconditionobject) команду, чтобы определить условие соответствия IP-адрес. В следующем примере замените *ip адрес диапазона-1*, *ip адрес диапазона-2* с собственного диапазона.

```powershell
  $IPMatchCondition = New-AzFrontDoorWafMatchConditionObject `
    -MatchVariable  RemoteAddr `
    -OperatorProperty IPMatch `
    -MatchValue ["ip-address-range-1", "ip-address-range-2"]
```
Создать сопоставления IP-адрес всех условное правило
```powershell
  $IPMatchALlCondition = New-AzFrontDoorWafMatchConditionObject `
    -MatchVariable  RemoteAddr `
    -OperatorProperty Any
    
```

### <a name="create-a-custom-ip-allow-rule"></a>Создание пользовательского правила разрешенных IP-адресов
   Используйте [New AzFrontDoorCustomRuleObject](/powershell/module/Az.FrontDoor/New-azfrontdoorwafcustomruleobject) команду, чтобы определить действие и установить приоритет. В следующем примере будут разрешены запросы от клиентских IP-адресов, соответствующих списку. 

```powershell
  $IPAllowRule = New-AzFrontDoorWafCustomRuleObject `
    -Name "IPAllowRule" `
    -RuleType MatchRule `
    -MatchCondition $IPMatchCondition `
    -Action Allow -Priority 1
```
Создайте блок все правила IP-адресов с низким приоритетом, чем предыдущий IP-адрес правила.

```powershell
  $IPBlockAll = New-AzFrontDoorWafCustomRuleObject `
    -Name "IPDenyAll" `
    -RuleType MatchRule `
    -MatchCondition $IPMatchALlCondition `
    -Action Block `
    -Priority 2
   ```

### <a name="configure-waf-policy"></a>Настройка политики WAF
С помощью команды `Get-AzResourceGroup` найдите имя группы ресурсов, содержащей профиль Front Door. Настройте политику WAF с правило блока IP-адрес с помощью [New AzFrontDoorWafPolicy](/powershell/module/az.frontdoor/new-azfrontdoorwafpolicy).

```powershell
  $IPAllowPolicyExamplePS = New-AzFrontDoorWafPolicy `
    -Name "IPRestrictionExamplePS" `
    -resourceGroupName <resource-group-name> `
    -Customrule $IPAllowRule $IPBlockAll `
    -Mode Prevention `
    -EnabledState Enabled
   ```

### <a name="link-waf-policy-to-a-front-door-front-end-host"></a>Ссылка WAF политики к узлу двери переднего плана

Свяжите объект политики WAF на существующий узел двери переднего плана и обновляет свойства двери. Сначала нужно получить объект двери с помощью [Get-AzFrontDoor](/powershell/module/Az.FrontDoor/Get-AzFrontDoor). Затем задайте интерфейсной части *WebApplicationFirewallPolicyLink* значение resourceId *$IPAllowPolicyExamplePS* создан на предыдущем шаге, с помощью [Set-AzFrontDoor](/powershell/module/Az.FrontDoor/Set-AzFrontDoor) команды.

```powershell
  $FrontDoorObjectExample = Get-AzFrontDoor `
    -ResourceGroupName <resource-group-name> `
    -Name $frontDoorName
  $FrontDoorObjectExample[0].FrontendEndpoints[0].WebApplicationFirewallPolicyLink = $IPBlockPolicy.Id
  Set-AzFrontDoor -InputObject $FrontDoorObjectExample[0]
```

> [!NOTE]
> В этом примере WAF политики применяется к FrontendEndpoints [0]. Политика WAF можно привязать к любому из внешних интерфейсов. Необходимо задать *WebApplicationFirewallPolicyLink* свойство один раз, чтобы связать политику WAF двери переднего плана. Последующие политики обновления автоматически применяются к внешней сети.


## <a name="configure-waf-policy-with-resource-manager-template"></a>Настройка политики WAF с помощью шаблона Resource Manager
Просмотреть шаблон, создающий двери и политику WAF с настраиваемые правила ограничения IP-адрес [здесь](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-waf-clientip).


## <a name="next-steps"></a>Дальнейшие действия

- Узнайте, как [создать профиль двери](quickstart-create-front-door.md).
