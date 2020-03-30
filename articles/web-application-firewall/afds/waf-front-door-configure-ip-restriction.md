---
title: Настройка правила ОГРАНИЧЕНИЯ IP-адреса WAF для передней двери Azure
description: Узнайте, как настроить правило «Файервол веб-приложений» для ограничения IP-адресов для существующей точки Azure Front Door.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.topic: article
ms.date: 03/26/2020
ms.author: tyao
ms.openlocfilehash: 077f127648688b25d45b433fa2bc94ee011b3f2d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80336070"
---
# <a name="configure-an-ip-restriction-rule-with-a-web-application-firewall-for-azure-front-door"></a>Налажить правило ограничения IP с помощью брандмауэра Web-приложения для передней двери Azure

В этой статье показано, как настроить правила ограничения IP в веб-приложении Firewall (WAF) для передней двери Azure с помощью портала Azure, Azure CLI, Azure PowerShell или шаблона управления ресурсами Azure.

Правило управления доступом на основе IP-адреса — это специальное правило WAF, которое позволяет контролировать доступ к веб-приложениям. Он делает это, указывая список IP-адресов или диапазонов IP-адресов в формате Classless Inter-Domain Routing (CIDR).

По умолчанию ваше веб-приложение доступно из Интернета. Если вы хотите ограничить доступ к клиентам из списка известных IP-адресов или IP-адресов, вы можете создать правило сопоставления IP, которое содержит список IP-адресов в качестве соответствующих значений и устанавливает оператора на "Not" (negate is true) и действие для **блокировки.** После применения правила ограничения IP запросы, поступающие с адресов за пределами этого разрешенного списка, получают 403 Запретный ответ.

## <a name="configure-a-waf-policy-with-the-azure-portal"></a>Настройка политики WAF с помощью портала Azure

### <a name="prerequisites"></a>Предварительные требования

Создайте профиль Azure Front Door, следуя инструкциям, описанным в [книге «Быстрый старт: Создайте переднюю дверь» для высокодоступного глобального веб-приложения.](../../frontdoor/quickstart-create-front-door.md)

### <a name="create-a-waf-policy"></a>Создание политики WAF

1. На портале Azure выберите **Создать ресурс,** введите **брандмауэр веб-приложений** в поле поиска, а затем выберите **web Application Firewall (WAF).**
2. Выберите **Создать**.
3. На странице **политики «Создайте» политику WAF** используйте следующие значения для заполнения **вкладки «Основы»:**
   
   |Параметр  |Значение  |
   |---------|---------|
   |Политика для     |Глобальный WAF (Фронтовая дверь)|
   |Подписка     |Выберите свою подписку.|
   |Группа ресурсов     |Выберите группу ресурсов, где находится ваша передняя дверь.|
   |Имя политики     |Введите имя для политики|
   |Состояние политики     |Активировано|

   Выберите **далее: Параметры политики**

1. На **вкладке параметры политики** выберите **«Предупреждение».** Для **тела ответа Блока,** тип *Вы были заблокированы!* так что вы можете видеть, что ваше пользовательское правило действует.
2. Выберите **далее: Управляемые правила**.
3. Выберите **Далее: Пользовательские правила**.
4. Выберите **Добавить пользовательское правило.**
5. На странице **пользовательского правила Добавить** используйте следующие значения теста для создания пользовательского правила:

   |Параметр  |Значение  |
   |---------|---------|
   |Имя пользовательского правила     |FdWafCustRule|
   |Состояние     |Активировано|
   |Тип правила     |Соответствует|
   |Приоритет    |100|
   |Тип соответствия     |IP-адрес|
   |Переменная соответствия|RemoteAddr|
   |Операция|Не содержит|
   |IP-адрес или диапазон|10.10.10.0/24|
   |То|Отказать в движении|

   :::image type="content" source="../media/waf-front-door-configure-ip-restriction/custom-rule.png" alt-text="Пользовательское правило":::

   Нажмите кнопку **Добавить**.
6. Выберите **Далее: Ассоциация**.
7. Выберите **Добавить передний хост.**
8. Для **хостов Frontend**выберите передний хост и выберите **Добавить.**
9. Выберите **Review + create** (Просмотреть и создать).
10. После того, как проверка политики пройдет, выберите **Create**.

### <a name="test-your-waf-policy"></a>Проверьте свою политику WAF

1. После завершения развертывания политики WAF просмотрите имя переднего узла Frontdoor Frontend.
2. Вы должны увидеть пользовательское сообщение блока.

   :::image type="content" source="../media/waf-front-door-configure-ip-restriction/waf-rule-test.png" alt-text="Тест правил WAF":::

   > [!NOTE]
   > Частный IP-адрес был намеренно использован в пользовательском правиле, чтобы гарантировать, что правило срабатывает. При фактическом развертывании создайте *правила,* *использующие* IP-адреса для вашей конкретной ситуации.

## <a name="configure-a-waf-policy-with-the-azure-cli"></a>Настройка политики WAF с помощью Azure CLI

### <a name="prerequisites"></a>Предварительные требования
Прежде чем приступить к настройке политики ограничения IP, навяжжните среду CLI и создайте профиль Azure Front Door.

#### <a name="set-up-the-azure-cli-environment"></a>Настройка среды Azure CLI
1. Установите [Azure CLI](/cli/azure/install-azure-cli)или используйте лазурную оболочку. Azure Cloud Shell — это бесплатная оболочка Bash, которую можно запускать непосредственно на портале Azure. Она включает предварительно установленный интерфейс Azure CLI и настроена для использования с вашей учетной записью. Выберите кнопку **«Попробуйте ее»** в последующих командах CLI, а затем войдите в учетную запись Azure в открытии сеанса облачной оболочки. После начала сеанса `az extension add --name front-door` введите, чтобы добавить расширение Azure Front Door.
 2. Если вы используете CLI локально в Bash, восвайтесь в Azure с помощью `az login`.

#### <a name="create-an-azure-front-door-profile"></a>Создание профиля передний двери Azure
Создайте профиль Azure Front Door, следуя инструкциям, описанным в [книге «Быстрый старт: Создайте переднюю дверь» для высокодоступного глобального веб-приложения.](../../frontdoor/quickstart-create-front-door.md)

### <a name="create-a-waf-policy"></a>Создание политики WAF

Создайте политику WAF, используя команду создания [команды a network front-door waf-политики.](/cli/azure/ext/front-door/network/front-door/waf-policy?view=azure-cli-latest#ext-front-door-az-network-front-door-waf-policy-create) В следующем примере замените имя политики *IPAllowPolicyExampleCLI* уникальным именем политики.

```azurecli-interactive 
az network front-door waf-policy create \
  --resource-group <resource-group-name> \
  --subscription <subscription ID> \
  --name IPAllowPolicyExampleCLI
  ```
### <a name="add-a-custom-ip-access-control-rule"></a>Добавить пользовательское правило управления доступом к IP

Используйте настройки [пользовательского правила waf-политики аз сети,](/cli/azure/ext/front-door/network/front-door/waf-policy/rule?view=azure-cli-latest#ext-front-door-az-network-front-door-waf-policy-rule-create) чтобы добавить специальное правило управления доступом IP для только что созданной политики WAF.

В следующих примерах:
-  Замените *IPAllowPolicyExampleCLI* своей уникальной политикой, созданной ранее.
-  Замените *IP-адрес-диапазон-1,* *ip-адрес-диапазон-2* с вашим собственным диапазоном.

Во-первых, создать правило IP-разрешить для политики, созданной на предыдущем этапе. 
> [!NOTE]
> **--отсрочка** необходима, потому что правило должно иметь условие соответствия, которое должно быть добавлено на следующем этапе.

```azurecli
az network front-door waf-policy rule create \
  --name IPAllowListRule \
  --priority 1 \
  --rule-type MatchRule \
  --action Block \
  --resource-group <resource-group-name> \
  --policy-name IPAllowPolicyExampleCLI --defer
```
Затем добавьте условие соответствия к правилу:

```azurecli
az network front-door waf-policy rule match-condition add \
--match-variable RemoteAddr \
--operator IPMatch \
--values "ip-address-range-1" "ip-address-range-2" \
--negate true \
--name IPAllowListRule \
  --resource-group <resource-group-name> \
  --policy-name IPAllowPolicyExampleCLI 
  ```
                                                   
### <a name="find-the-id-of-a-waf-policy"></a>Поиск идентификатора политики WAF 
Найдите идентификатор политики WAF, используя команду [шоу-шоу anetwork front-door waf-политики.](/cli/azure/ext/front-door/network/front-door/waf-policy?view=azure-cli-latest#ext-front-door-az-network-front-door-waf-policy-show) Замените *IPAllowPolicyExampleCLI* в следующем примере своей уникальной политикой, созданной ранее.

   ```azurecli
   az network front-door  waf-policy show \
     --resource-group <resource-group-name> \
     --name IPAllowPolicyExampleCLI
   ```

### <a name="link-a-waf-policy-to-an-azure-front-door-front-end-host"></a>Связать политику WAF с передним хостом Azure Front Door

Установите идентификатор Azure Front Door *WebApplicationWallPolicyLink* ID на идентификатор политики с помощью команды [обновления передних дверей сети az.](/cli/azure/ext/front-door/network/front-door?view=azure-cli-latest#ext-front-door-az-network-front-door-update) Замените *IPAllowPolicyExampleCLI* своей уникальной политикой, созданной ранее.

   ```azurecli
   az network front-door update \
     --set FrontendEndpoints[0].WebApplicationFirewallPolicyLink.id=/subscriptions/<subscription ID>/resourcegroups/resource-group-name/providers/Microsoft.Network/frontdoorwebapplicationfirewallpolicies/IPAllowPolicyExampleCLI \
     --name <frontdoor-name>
     --resource-group <resource-group-name>
   ```
В этом примере политика WAF применяется к **Frontendpoints**. Вы можете связать политику WAF с любым из ваших передних концов.
> [!Note]
> Необходимо установить свойство **WebApplicationFirewallPolicyLink** только один раз, чтобы связать политику WAF с передней частью передней двери Azure. Последующие обновления политики автоматически применяются к передней части.

## <a name="configure-a-waf-policy-with-azure-powershell"></a>Налажить политику WAF с помощью Azure PowerShell

### <a name="prerequisites"></a>Предварительные требования
Прежде чем приступить к настройке политики ограничения IP, навяжжните среду PowerShell и создайте профиль Azure Front Door.

#### <a name="set-up-your-powershell-environment"></a>Настройка среды PowerShell
Azure PowerShell предоставляет набор cmdlets, которые используют модель [управления ресурсами Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) для управления ресурсами Azure.

Вы можете установить [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) на локальном компьютере и использовать его в любом сеансе PowerShell. Следуйте инструкциям на странице, чтобы войти в PowerShell, используя учетные данные Azure, а затем установите модуль Az.

1. Подключитесь к Azure, используя следующую команду, а затем используйте интерактивный диалог для входа.
    ```
    Connect-AzAccount
    ```
 2. Перед установкой модуля Azure Front Door убедитесь, что у вас установлена текущая версия модуля PowerShellGet. Выполнить следующую команду, а затем вновь открыть PowerShell.

    ```
    Install-Module PowerShellGet -Force -AllowClobber
    ``` 

3. Установите модуль Az.FrontDoor с помощью следующей команды. 
    
    ```
    Install-Module -Name Az.FrontDoor
    ```
### <a name="create-an-azure-front-door-profile"></a>Создание профиля передний двери Azure
Создайте профиль Azure Front Door, следуя инструкциям, описанным в [книге «Быстрый старт: Создайте переднюю дверь» для высокодоступного глобального веб-приложения.](../../frontdoor/quickstart-create-front-door.md)

### <a name="define-an-ip-match-condition"></a>Определение состояния ip-соответствия
Для определения состояния IP-соответствия используйте команду [New-AzFrontDoorWafMatchConditionObject.](/powershell/module/az.frontdoor/new-azfrontdoorwafmatchconditionobject)
В следующем примере замените *ip-адрес-диапазон-1,* *ip-адрес-диапазон-2* с вашим собственным диапазоном.    
```powershell
$IPMatchCondition = New-AzFrontDoorWafMatchConditionObject `
-MatchVariable  RemoteAddr `
-OperatorProperty IPMatch `
-MatchValue "ip-address-range-1", "ip-address-range-2"
-NegateCondition 1
```
     
### <a name="create-a-custom-ip-allow-rule"></a>Создание пользовательского правила IP-размноза

Используйте команду [New-AzFrontDoorWafCustomRuleObject,](/powershell/module/Az.FrontDoor/New-azfrontdoorwafcustomruleobject) чтобы определить действие и установить приоритет. В следующем примере запросы не от ис-ипотечники клиентов, которые соответствуют списку, будут заблокированы.

```azurepowershell
$IPAllowRule = New-AzFrontDoorWafCustomRuleObject `
-Name "IPAllowRule" `
-RuleType MatchRule `
-MatchCondition $IPMatchCondition `
-Action Block -Priority 1
```

### <a name="configure-a-waf-policy"></a>Настройка политики WAF
Найдите имя группы ресурсов, содержащей профиль `Get-AzResourceGroup`Azure Front Door с помощью . Затем направите политику WAF с правилом IP с помощью [New-AzFrontDoorWafPolicy.](/powershell/module/az.frontdoor/new-azfrontdoorwafpolicy)

```azurepowershell
  $IPAllowPolicyExamplePS = New-AzFrontDoorWafPolicy `
    -Name "IPRestrictionExamplePS" `
    -resourceGroupName <resource-group-name> `
    -Customrule $IPAllowRule`
    -Mode Prevention `
    -EnabledState Enabled
   ```

### <a name="link-a-waf-policy-to-an-azure-front-door-front-end-host"></a>Связать политику WAF с передним хостом Azure Front Door

Свяжите объект политики WAF с существующим передним узлам и обновите свойства Azure Front Door. Во-первых, получить объект Передней двери Azure с помощью [Get-AzFrontDoor](/powershell/module/Az.FrontDoor/Get-AzFrontDoor). Затем установите свойство **WebApplicationFirewallPolicyLink** в идентификатор ресурсов *$IPAllowPolicyExamplePS,* созданный на предыдущем этапе, с помощью команды [Set-AzFrontDoor.](/powershell/module/Az.FrontDoor/Set-AzFrontDoor)

```azurepowershell
  $FrontDoorObjectExample = Get-AzFrontDoor `
    -ResourceGroupName <resource-group-name> `
    -Name $frontDoorName
  $FrontDoorObjectExample[0].FrontendEndpoints[0].WebApplicationFirewallPolicyLink = $IPBlockPolicy.Id
  Set-AzFrontDoor -InputObject $FrontDoorObjectExample[0]
```

> [!NOTE]
> В этом примере политика WAF применяется к **Frontendpoints**. Вы можете связать политику WAF с любым из ваших передних концов. Необходимо установить свойство **WebApplicationFirewallPolicyLink** только один раз, чтобы связать политику WAF с передней частью передней двери Azure. Последующие обновления политики автоматически применяются к передней части.


## <a name="configure-a-waf-policy-with-a-resource-manager-template"></a>Настройка политики WAF с шаблоном менеджера ресурсов
Чтобы просмотреть шаблон, создающий политику Azure Front Door и политику WAF с пользовательскими правилами ограничения IP, перейдите на [GitHub.](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-waf-clientip)


## <a name="next-steps"></a>Дальнейшие действия

- Узнайте, как [создать профиль Передней двери Azure.](../../frontdoor/quickstart-create-front-door.md)
