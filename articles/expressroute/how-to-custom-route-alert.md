---
title: 'ExpressRoute: Настройка пользовательских оповещений для объявленных маршрутов'
description: В этой статье показано, как с помощью службы автоматизации Azure и Logic Apps отслеживать количество маршрутов, объявляемых из шлюза ExpressRoute в локальные сети, чтобы предотвратить попадание в пределы маршрутов 200.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 05/29/2020
ms.author: duau
ms.openlocfilehash: b1b93110c3dba38dadf7079fc24ba12e81793c02
ms.sourcegitcommit: 03713bf705301e7f567010714beb236e7c8cee6f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/21/2020
ms.locfileid: "92329855"
---
# <a name="configure-custom-alerts-to-monitor-advertised-routes"></a>Настройка пользовательских оповещений для отслеживания объявляемых маршрутов

Эта статья поможет вам использовать службу автоматизации Azure и Logic Apps для постоянного наблюдения за числом маршрутов, объявленных из шлюза ExpressRoute в локальные сети. Мониторинг может помочь в предотвращении попадания в [пределы 200 маршрутов](expressroute-faqs.md#how-many-prefixes-can-be-advertised-from-a-vnet-to-on-premises-on-expressroute-private-peering).

Служба **автоматизации Azure** позволяет автоматизировать выполнение пользовательского скрипта PowerShell, хранящегося в *модуле Runbook*. При использовании конфигурации в этой статье модуль Runbook содержит сценарий PowerShell, который запрашивает один или несколько шлюзов ExpressRoute. Он собирает набор данных, содержащий группу ресурсов, имя шлюза ExpressRoute и число префиксов сети, объявленных локально.

**Azure Logic Apps** планирует пользовательский рабочий процесс, который вызывает Runbook службы автоматизации Azure. Выполнение модуля Runbook выполняется с помощью задания. После выполнения сбора данных рабочий процесс Azure Logic Apps классифицирует данные и, основываясь на критериях соответствия для числа префиксов сети выше или ниже порога предопределения, отправляет сведения на адрес электронной почты назначения.

### <a name="workflow"></a><a name="workflow"></a>Рабочий процесс

Настройка настраиваемого оповещения осуществляется на основе трех основных этапов:

1. Создайте учетную запись службы автоматизации с учетной записью запуска от имени и разрешениями.

2. Создание и Настройка модулей Runbook.

3. Создайте приложение логики, которое будет запускать учетную запись службы автоматизации и отправлять оповещения по электронной почте, если число превышает пороговое значение (например, 160).

## <a name="before-you-begin"></a><a name="before"></a>Перед началом

Перед началом настройки убедитесь, что удовлетворены следующие требования:

* В развертывании имеется по крайней мере один шлюз ExpressRoute.

* У вас есть базовое представление о [учетных записях запуска от имени](../automation/manage-runas-account.md) в службе автоматизации Azure.

* Вы знакомы с [Azure Logic Apps](../logic-apps/logic-apps-overview.md).

* Вы знакомы с использованием Azure PowerShell. Azure PowerShell требуется для получения префиксов сети в шлюзе ExpressRoute. Дополнительные сведения о Azure PowerShell в целом см. в [документации по Azure PowerShell](/powershell/azure/?view=azps-4.1.0).

### <a name="notes-and-limitations"></a><a name="limitations"></a>Примечания и ограничения

* Настраиваемое оповещение, описанное в этой статье, является надстройкой для достижения лучшей работы и контроля. Это не замена собственных оповещений в ExpressRoute.
* Сбор данных для шлюзов ExpressRoute выполняется в фоновом режиме. Время выполнения может быть больше ожидаемого. Чтобы избежать очереди заданий, необходимо правильно настроить повторение рабочего процесса.
* Развертывания с помощью скриптов или шаблонов ARM могут происходить быстрее, чем пользовательский триггер будильника. Это может привести к увеличению числа префиксов сети в шлюзе ExpressRoute, превышающем ограничение в 200 маршрутов.

## <a name="create-and-configure-accounts"></a><a name="accounts"></a>Создание и Настройка учетных записей

При создании учетной записи службы автоматизации в портал Azure автоматически создается учетная запись [запуска от имени](../automation/automation-security-overview.md#run-as-accounts) . Эта учетная запись принимает следующие действия:

* Создает приложение Azure Active Directory (Azure AD) с самозаверяющим сертификатом. Сама учетная запись запуска от имени имеет сертификат, который необходимо обновлять по умолчанию каждый год.

* Создает учетную запись субъекта-службы для приложения в Azure AD.

* Назначает себе роль участника (Azure RBAC) в используемой подписке Azure. Эта роль управляет ресурсами Azure Resource Manager с помощью модулей Runbook.

Чтобы создать учетную запись службы автоматизации, необходимы права и разрешения. Дополнительные сведения см. [в разделе разрешения, необходимые для создания учетной записи службы автоматизации](../automation/automation-create-standalone-account.md#permissions-required-to-create-an-automation-account).

### <a name="1-create-an-automation-account"></a><a name="about"></a>1. Создание учетной записи службы автоматизации

Создайте учетную запись службы автоматизации с разрешениями Запуск от имени. Инструкции см. в статье [Создание учетной записи службы автоматизации Azure](../automation/automation-quickstart-create-account.md).

:::image type="content" source="./media/custom-route-alert-portal/create-account.png" alt-text="Добавить учетную запись службы автоматизации" lightbox="./media/custom-route-alert-portal/create-account-expand.png":::

### <a name="2-assign-the-run-as-account-a-role"></a><a name="about"></a>2. Назначение роли учетной записи запуска от имени

По умолчанию роль **участника** назначается субъекту-службе, используемому учетной записью **запуска от имени** . Можно защитить роль по умолчанию, назначенную субъекту-службе, или ограничить разрешения, назначив [встроенную роль](../role-based-access-control/built-in-roles.md) (например, читатель) или [настраиваемую роль](../active-directory/users-groups-roles/roles-create-custom.md).

 Чтобы определить роль, назначенную субъекту-службе, используемому учетной записью запуска от имени, выполните следующие действия.

1. Перейдите к учетной записи службы автоматизации. Перейдите к **параметрам учетной записи**, а затем выберите **учетные записи запуска от имени**.

2. Выберите **роли** , чтобы просмотреть используемое определение роли.

   :::image type="content" source="./media/custom-route-alert-portal/run-as-account-permissions.png" alt-text="Добавить учетную запись службы автоматизации":::

## <a name="create-and-configure-runbooks"></a><a name="runbooks"></a>Создание и Настройка модулей Runbook

### <a name="1-install-modules"></a><a name="install-modules"></a>1. Установка модулей

Чтобы запустить командлеты PowerShell в модулях Runbook службы автоматизации Azure, необходимо установить несколько дополнительных модулей Azure PowerShell AZ. Чтобы установить модули, выполните следующие действия.

1. Откройте учетную запись службы автоматизации Azure и перейдите к разделу **модули**.

   :::image type="content" source="./media/custom-route-alert-portal/navigate-modules.png" alt-text="Добавить учетную запись службы автоматизации":::

2. Выполните поиск в коллекции и импортируйте следующие модули: **AZ. Accounts**, **AZ. Network**, **AZ. Automation**и **AZ. Profile**.

   :::image type="content" source="./media/custom-route-alert-portal/import-modules.png" alt-text="Добавить учетную запись службы автоматизации" lightbox="./media/custom-route-alert-portal/import-modules-expand.png":::
  
### <a name="2-create-a-runbook"></a><a name="create"></a>2. Создание модуля Runbook

1. Чтобы создать модуль Runbook PowerShell, перейдите к учетной записи службы автоматизации. В разделе **Автоматизация процессов**выберите плитку **модули Runbook** , а затем щелкните **создать Runbook**.

   :::image type="content" source="./media/custom-route-alert-portal/create-runbook.png" alt-text="Добавить учетную запись службы автоматизации":::

2. Выберите **создать** , чтобы создать модуль Runbook.

   :::image type="content" source="./media/custom-route-alert-portal/create-runbook-2.png" alt-text="Добавить учетную запись службы автоматизации":::

3. Выберите только что созданный модуль Runbook, а затем нажмите кнопку **изменить**.

   :::image type="content" source="./media/custom-route-alert-portal/edit-runbook.png" alt-text="Добавить учетную запись службы автоматизации":::

4. В поле **Правка**вставьте скрипт PowerShell. [Пример скрипта](#script) можно изменить и использовать для мониторинга шлюзов ExpressRoute в одной или нескольких группах ресурсов.

   В примере сценария Обратите внимание на следующие параметры:

    * Массив **$rgList** содержит список групп ресурсов с шлюзами ExpressRoute. Вы можете настроить шлюзы ExpressRoute на основе списка.
    * Переменная **$thresholdNumRoutes** определить пороговое значение числа сетевых префиксов, объявленных из шлюза ExpressRoute в локальные сети.

#### <a name="example-script"></a><a name="script"></a>Пример сценария

```powershell
  ################# Input parameters #################
# Resource Group Name where the ExR GWs resides in
$rgList= @('ASH-Cust10-02','ASH-Cust30')  
$thresholdNumRoutes = 160
###################################################

# Ensures you do not inherit an AzContext in your runbook
Disable-AzContextAutosave -Scope Process | Out-Null

Try {

   $conn = Get-AutomationConnection -Name 'AzureRunAsConnection'
   while(!($connectionResult) -And ($logonAttempt -le 5))
   {
        $LogonAttempt++
        # Logging in to Azure...
        $connectionResult =  Connect-AzAccount `
                               -ServicePrincipal `
                               -ApplicationId $conn.ApplicationId `
                               -Tenant $conn.TenantId `
                               -CertificateThumbprint $conn.CertificateThumbprint `
                               -Subscription $conn.SubscriptionId `
                               -Environment AzureCloud 
                               
        Start-Sleep -Seconds 10
    }
} Catch {
    if (!$conn)
    {
        $ErrorMessage = "Service principal not found."
        throw $ErrorMessage
    } 
    else
    {
        Write-Error -Message $_.Exception
        throw $_.Exception
    }
}

# Get the name of the Azure subscription
$subscriptionName=(Get-AzSubscription -SubscriptionId $conn.SubscriptionId).Name

#write-Output "<br>$(Get-Date) - selection of the Azure subscription: $subscriptionName" 
Select-AzSubscription -SubscriptionId $conn.SubscriptionId | Out-Null


$GtwList = @()
$results= @()

foreach ($rgName in $rgList)
{
## Collect all the ExpressRoute gateways in a Resource Group
$GtwList=Get-AzVirtualNetworkGateway -ResourceGroupName $rgName 

## For each ExpressRoute gateway, get the IP addresses of the BGP peers and collect the number of routes advertised 
foreach ($gw in $GtwList) {
  
  $peers = Get-AzVirtualNetworkGatewayBGPPeerStatus -VirtualNetworkGatewayName $gw.Name -ResourceGroupName $rgName


 if ($peers[0].State -eq 'Connected') {
   $routes1=$null
   $routes1 = Get-AzVirtualNetworkGatewayAdvertisedRoute -VirtualNetworkGatewayName $gw.Name -ResourceGroupName $rgName -Peer $peers[0].Neighbor
 }
  if ($peers[1].State -eq 'Connected') {
  
   $routes2=$null
   $routes2 = Get-AzVirtualNetworkGatewayAdvertisedRoute -VirtualNetworkGatewayName $gw.Name -ResourceGroupName $rgName -Peer $peers[1].Neighbor
 }
 
  $sampleTime=(Get-Date).ToString("dd-MM-yyyy HH:mm:ss")
  if ($routes1.Count -eq $routes2.Count)
  {
     
     if ($routes1.Count -lt $thresholdNumRoutes){
       $status='OK'
       $alertMsg='number of routes below threshold'
     } 
     else {
       $status='ALERT'
       $alertMsg='number of routes above threshold'
     }
  } 
  else
  {
     $status='WARNING'
     $alertMsg='check ER Gateway'
  }
  
  $obj = [psCustomObject]@{
            resourceGroup =$rgName
            nameGtw  = $gw.Name
            peer1 = $peers[0].Neighbor
            peer2 = $peers[1].Neighbor
            numRoutesPeer1=  $routes1.Count
            numRoutesPeer2=  $routes2.Count
            time=$sampleTime
            status=$status
            alertMessage = $alertMsg
        }
  $results += $obj
} ### end foreach gateways in each resource group
} ### end foreach resource group
$jsonResults= ConvertTo-Json $results -Depth 100
Write-Output  $jsonResults
 
 ```

### <a name="3-save-and-publish-the-runbook"></a><a name="publish"></a>3. Сохраните и опубликуйте Runbook

1. Нажмите кнопку **сохранить** , чтобы сохранить черновую копию модуля Runbook.
2. Выберите **опубликовать** , чтобы опубликовать модуль Runbook в качестве официальной версии модуля Runbook в учетной записи службы автоматизации.

   :::image type="content" source="./media/custom-route-alert-portal/save-publish-runbook.png" alt-text="Добавить учетную запись службы автоматизации":::

При запуске скрипта PowerShell выполняется сбор списка значений:
 
* Группа ресурсов

* Имя шлюза ExpressRoute

* IP-адрес первого узла BGP (Peer1)

* IP-адрес второго узла BGP (peer2)

* Число префиксов сети, объявленных из шлюза ExpressRoute на первый узел BGP (Peer1)

* Число префиксов сети, объявленных из шлюза ExpressRoute на второй узел BGP (peer2)

* Отметка времени

* Состояние, классифицировано как:

  * "ОК", если число маршрутов меньше порогового значения
  * "Предупреждение", если число маршрутов превышает пороговое значение
  * "WARNING", если число префиксов сети, объявленных на двух узлах BGP, отличается

* Предупреждающее сообщение для подробного описания состояния (ОК, предупреждение, предупреждение)

Сценарий PowerShell преобразует собранные данные в выходные данные JSON. Модуль Runbook использует командлет PowerShell [Write-Output](/powershell/module/Microsoft.PowerShell.Utility/Write-Output)  в качестве потока вывода для передачи информации клиенту.

### <a name="4-validate-the-runbook"></a><a name="validate"></a>4. Проверка модуля Runbook

После создания модуля Runbook его необходимо проверить. Выберите **запустить** и проверьте выходные данные и ошибки для разных потоков заданий.

:::image type="content" source="./media/custom-route-alert-portal/validate-runbook.png" alt-text="Добавить учетную запись службы автоматизации" lightbox="./media/custom-route-alert-portal/validate-runbook-expand.png":::

## <a name="create-and-configure-a-logic-app"></a><a name="logic"></a>Создание и настройка приложения логики

Azure Logic Apps — это Orchestrator для всех процессов сбора и действий. В следующих разделах описывается создание рабочего процесса с помощью приложения логики.

### <a name="workflow"></a>Рабочий процесс

В этом рабочем процессе создается приложение логики, которое регулярно отслеживает шлюзы ExpressRoute. Если они есть, приложение логики отправляет сообщение электронной почты для каждого из них. По завершении приложение логики будет выглядеть как этот высокоуровневый рабочий процесс:

:::image type="content" source="./media/custom-route-alert-portal/logic-apps-workflow.png" alt-text="Добавить учетную запись службы автоматизации":::

### <a name="1-create-a-logic-app"></a>1. Создание приложения логики

В **конструкторе приложений логики**Создайте приложение логики с помощью шаблона **пустое приложение логики** . Инструкции см. в разделе [Create Logic Apps](../logic-apps/quickstart-create-first-logic-app-workflow.md#create-your-logic-app).

:::image type="content" source="./media/custom-route-alert-portal/blank-template.png" alt-text="Добавить учетную запись службы автоматизации":::

### <a name="2-add-a-trigger"></a>2. добавление триггера

Каждое приложение логики запускается триггером. Триггер срабатывает при наступлении определенного события или при выполнении определенного условия. При каждом срабатывании триггера обработчик Azure Logic Apps создает экземпляр приложения логики, запускающий и выполняющий рабочий процесс.

Чтобы регулярно запускать приложение логики, основанное на предварительно определенном расписании времени, Добавьте встроенное **Расписание повторения:** в рабочий процесс. В поле поиска введите **Schedule**. Выберите **Триггеры**. В списке триггеров выберите **Расписание повторений**.

:::image type="content" source="./media/custom-route-alert-portal/schedule.png" alt-text="Добавить учетную запись службы автоматизации":::

В триггере расписания повторения можно задать часовой пояс и повторение для повторения этого рабочего процесса. Совместно интервал и частота определяют расписание для триггера приложения логики. Чтобы установить разумную минимальную частоту повторений, учитывайте следующие факторы.

* Для выполнения скрипта PowerShell в Runbook службы автоматизации требуется время. Среда выполнения зависит от числа шлюзов ExpressRoute для мониторинга. Слишком короткий интервал повторения приведет к постановке в очередь заданий.

* Сценарий PowerShell выполняется как задание в фоновом режиме. Он не запускается немедленно; он выполняется после переменной задержки.

* Слишком короткий интервал повторения приведет к ненужной нагрузке на шлюзы Azure ExpressRoute.

В конце конфигурации рабочего процесса можно проверить согласованность периодичности повторения, запустив рабочий процесс несколько раз, а затем проверив результат в **журнале выполнения**.

:::image type="content" source="./media/custom-route-alert-portal/recurrence.png" alt-text="Добавить учетную запись службы автоматизации" lightbox="./media/custom-route-alert-portal/recurrence-expand.png":::

### <a name="3-create-a-job"></a><a name="job"></a>3. Создание задания

Приложение логики обращается к другим приложениям, службам и платформам, хотя соединители. Следующим шагом в этом рабочем процессе является выбор соединителя для доступа к учетной записи службы автоматизации Azure, которая была определена ранее.

1. В **Logic Apps конструкторе**ниже **повторение**выберите **новый шаг**. В разделе **Выбор действия** и поля поиска выберите **все**.
2. В поле поиска введите Служба **автоматизации Azure** и поиск. Выберите **создать задание**. **Создание задания** будет использоваться для запуска модуля Runbook службы автоматизации, созданного ранее.

   :::image type="content" source="./media/custom-route-alert-portal/create-job.png" alt-text="Добавить учетную запись службы автоматизации":::

3. Войдите с помощью субъекта-службы. Вы можете использовать существующий субъект-службу или создать новый. Сведения о создании субъекта-службы см. в статье [Использование портала для создания субъекта-службы Azure AD, который имеет доступ к ресурсам](../active-directory/develop/howto-create-service-principal-portal.md). Выберите **подключиться с помощью субъекта-службы**.

   :::image type="content" source="./media/custom-route-alert-portal/sign-in.png" alt-text="Добавить учетную запись службы автоматизации":::

4. Введите **имя подключения**, добавьте **идентификатор клиента** (идентификатор приложения), **секрет клиента**и **идентификатор**клиента. Затем выберите **Создать**.

   :::image type="content" source="./media/custom-route-alert-portal/connect-service-principal.png" alt-text="Добавить учетную запись службы автоматизации" для **учетной записи службы автоматизации**. Кроме того, убедитесь, что **имя Runbook** было добавлено в качестве нового параметра.

   :::image type="content" source="./media/custom-route-alert-portal/roles.png" alt-text="Добавить учетную запись службы автоматизации" lightbox="./media/custom-route-alert-portal/roles-expand.png":::

### <a name="4-get-the-job-output"></a><a name="output"></a>4. получение выходных данных задания

1. Выберите **Новый шаг**. Выполните поиск по запросу "служба автоматизации Azure". В списке **действия** выберите **получить выходные данные задания**.

   :::image type="content" source="./media/custom-route-alert-portal/get-output.png" alt-text="Добавить учетную запись службы автоматизации":::

2. На странице **получение выходных данных задания** укажите сведения, необходимые для доступа к учетной записи службы автоматизации. Выберите **подписку, группу ресурсов**и **учетную запись службы автоматизации** , которую вы хотите использовать. Щелкните внутри поля **идентификатор задания** . Когда появится список **динамического содержимого** , выберите **идентификатор задания**.

   :::image type="content" source="./media/custom-route-alert-portal/job-id.png" alt-text="Добавить учетную запись службы автоматизации" lightbox="./media/custom-route-alert-portal/job-id-expand.png":::

### <a name="5-parse-the-json"></a><a name="parse"></a>5. анализ JSON

Сведения, содержащиеся в выходных данных из действия "Создание задания службы автоматизации Azure" (предыдущие шаги), формируют объект JSON. Logic Apps **анализ JSON** — это встроенное действие для создания понятных пользователю маркеров из свойств и их значений в содержимом JSON. Затем эти свойства можно использовать в рабочем процессе.

1. Добавление действия. В **действии получение выходных данных задания — >** выберите **новый шаг**.
2. В поле поиска **Выбор действия** введите "анализ JSON", чтобы найти соединители, которые предлагают это действие. В списке **действия** выберите действие **анализ JSON** для операций с данными, которые необходимо использовать.

   :::image type="content" source="./media/custom-route-alert-portal/parse-json.png" alt-text="Добавить учетную запись службы автоматизации":::

3. Щелкните внутри поля **содержимое** . Когда появится список динамического содержимого, выберите **содержимое**.

   :::image type="content" source="./media/custom-route-alert-portal/content.png" alt-text="Добавить учетную запись службы автоматизации" lightbox="./media/custom-route-alert-portal/content-expand.png":::

4. Для синтаксического анализа JSON требуется схема. Схему можно создать с помощью выходных данных модуля Runbook службы автоматизации. Откройте новый сеанс веб-браузера, запустите модуль Runbook службы автоматизации и возьмите выходные данные. Вернитесь к действию **операций Logic Apps анализ данных JSON** . В нижней части страницы выберите **использовать образец полезных данных для создания схемы**.

   :::image type="content" source="./media/custom-route-alert-portal/sample-payload.png" alt-text="Добавить учетную запись службы автоматизации":::

5. Для **ввода или вставки образца полезных данных JSON**вставьте выходные данные модуля Runbook службы автоматизации и нажмите кнопку **Готово**.

   :::image type="content" source="./media/custom-route-alert-portal/paste-payload.png" alt-text="Добавить учетную запись службы автоматизации" lightbox="./media/custom-route-alert-portal/paste-payload-expand.png":::

6. Схема создается автоматически путем анализа входных данных JSON.

   :::image type="content" source="./media/custom-route-alert-portal/generate-schema.png" alt-text="Добавить учетную запись службы автоматизации" lightbox="./media/custom-route-alert-portal/generate-schema-expand.png":::

### <a name="6-define-and-initialize-a-variable"></a><a name="define-variable"></a>6. Определение и инициализация переменной

На этом шаге рабочего процесса мы создадим условие для отправки оповещения по электронной почте. Для гибкого пользовательского форматирования сообщения электронной почты вспомогательная переменная вводится в рабочий процесс.

1. В **действии получение выходных данных задания**выберите **новый шаг**. В поле поиска найдите и выберите **переменные**.

   :::image type="content" source="./media/custom-route-alert-portal/variables.png" alt-text="Добавить учетную запись службы автоматизации":::

2. В списке **действия** выберите действие **Инициализация переменной** .

   :::image type="content" source="./media/custom-route-alert-portal/initialize-variables.png" alt-text="Добавить учетную запись службы автоматизации":::

3. Укажите имя переменной. В качестве **типа**выберите **строка**. **Значение** переменной будет присвоено позже в рабочем процессе.

   :::image type="content" source="./media/custom-route-alert-portal/string.png" alt-text="Добавить учетную запись службы автоматизации" lightbox="./media/custom-route-alert-portal/string-expand.png":::

### <a name="7-create-a-for-each-action"></a><a name="cycles-json"></a>7. Создание действия "для каждого"

После синтаксического анализа JSON действие **операция анализа данных JSON** сохраняет содержимое в выходном *тексте* . Для обработки выходных данных можно создать цикл for each, повторив одно или несколько действий с каждым элементом в массиве.

1. В разделе **инициализировать переменную**выберите **Добавить действие**. В поле поиска введите for each в качестве условия фильтрации.

   :::image type="content" source="./media/custom-route-alert-portal/control.png" alt-text="Добавить учетную запись службы автоматизации":::

2. В списке **действия** выберите действие **для каждого элемента управления**.

   :::image type="content" source="./media/custom-route-alert-portal/for-each.png" alt-text="Добавить учетную запись службы автоматизации":::

3. Щелкните текстовое поле **выберите выходные данные из предыдущих шагов** . Когда появится список **динамического содержимого** , выберите **текст**, который выводится из проанализированного JSON.

   :::image type="content" source="./media/custom-route-alert-portal/body.png" alt-text="Добавить учетную запись службы автоматизации":::

4. Для каждого элемента тела JSON необходимо задать условие. В группе Действие выберите **элемент Управление**.

   :::image type="content" source="./media/custom-route-alert-portal/condition-control.png" alt-text="Добавить учетную запись службы автоматизации":::

5. В списке **действия** выберите **элемент Управление условием**. Condition-Control является структурой управления, сравнивает данные в рабочем процессе с конкретными значениями или полями. Затем можно указать различные действия, выполняемые в зависимости от того, соответствуют ли данные условию.

   :::image type="content" source="./media/custom-route-alert-portal/condition.png" alt-text="Добавить учетную запись службы автоматизации":::

6. В поле корень действия **условия** измените логическую операцию на **или**.

   :::image type="content" source="./media/custom-route-alert-portal/condition-or.png" alt-text="Добавить учетную запись службы автоматизации" lightbox="./media/custom-route-alert-portal/condition-or-expand.png":::

7. Проверьте значение числа префиксов сети, которые шлюз ExpressRoute объявляет двум одноранговым узлам BGP. Число маршрутов доступно в "numRoutePeer1" и "numRoutePeer2" в **динамическом содержимом**. В поле значение введите значение для **numRoutePeer1**.

   :::image type="content" source="./media/custom-route-alert-portal/peer-1.png" alt-text="Добавить учетную запись службы автоматизации":::

8. Чтобы добавить в условие другую строку, выберите **Добавить-> добавить строку**. Во втором поле из **динамического содержимого**выберите **numRoutePeer2**.

   :::image type="content" source="./media/custom-route-alert-portal/peer-2.png" alt-text="Добавить учетную запись службы автоматизации":::

9. Условие логики имеет значение true, если одна из двух динамических переменных, numRoute1 или numRoute2, больше порогового значения. В этом примере пороговое значение фиксировано до 160 (80% от максимального значения, равного 200 маршрутов). Пороговое значение можно изменить в соответствии с вашими требованиями. Для обеспечения согласованности значение должно иметь то же значение, которое использовалось в скрипте Runbook PowerShell.

   :::image type="content" source="./media/custom-route-alert-portal/logic-condition.png" alt-text="Добавить учетную запись службы автоматизации":::

10. В разделе **при значении true**отформатируйте и создайте действия для отправки предупреждения по электронной почте. В * * выберите действие, найдите и выберите **переменные**.

    :::image type="content" source="./media/custom-route-alert-portal/condition-if-true.png" alt-text="Добавить учетную запись службы автоматизации":::

11. В списке переменные выберите **Добавить действие**. В списке **действия** выберите **задать переменную**.

    :::image type="content" source="./media/custom-route-alert-portal/condition-set-variable.png" alt-text="Добавить учетную запись службы автоматизации":::

12. В поле **имя**выберите переменную с именем **емаилбоди** , созданную ранее. В поле **значение**вставьте скрипт HTML, необходимый для форматирования электронного сообщения с оповещением. Используйте **динамическое содержимое** для включения значений текста JSON. После настройки этих параметров результат заключается в том, что переменная **емаилбоди** содержит все сведения, относящиеся к предупреждению, в формате HTML.

    :::image type="content" source="./media/custom-route-alert-portal/paste-script.png" alt-text="Добавить учетную запись службы автоматизации":::

### <a name="8-add-the-email-connector"></a><a name="email"></a>8. Добавление соединителя электронной почты

Logic Apps предоставляет много соединителей электронной почты. В этом примере мы добавим соединитель Outlook для отправки оповещения по электронной почте. В разделе **задать переменную**выберите **Добавить действие**. В окне **Выбор действия**введите в поле поиска команду "отправить электронное письмо".

1. Выберите **Office 365 Outlook**.

   :::image type="content" source="./media/custom-route-alert-portal/email.png" alt-text="Добавить учетную запись службы автоматизации":::

2. В списке **действия** выберите **Отправить сообщение электронной почты (v2)**.

   :::image type="content" source="./media/custom-route-alert-portal/email-v2.png" alt-text="Добавить учетную запись службы автоматизации":::

3. Войдите в систему, чтобы создать подключение к Office 365 Outlook.

   :::image type="content" source="./media/custom-route-alert-portal/office-365.png" alt-text="Добавить учетную запись службы автоматизации":::

4. В поле **Body (текст** ) щелкните **добавить динамическое содержимое**. На панели динамического содержимого добавьте переменную **емаилбоди**. Заполните **Subject** поля Тема **To** и.

   :::image type="content" source="./media/custom-route-alert-portal/emailbody.png" alt-text="Добавить учетную запись службы автоматизации":::

5. Действие **Отправить электронное письмо (v2)** завершить настройку рабочего процесса.

   :::image type="content" source="./media/custom-route-alert-portal/send-email-v2.png" alt-text="Добавить учетную запись службы автоматизации" lightbox="./media/custom-route-alert-portal/send-email-v2-expand.png":::

### <a name="9-workflow-validation"></a><a name="validation"></a>9. Проверка рабочего процесса

Последним шагом является проверка рабочего процесса. В **Logic Apps обзор**выберите **запустить триггер**. Выберите **повторение**. Рабочий процесс можно отслеживать и проверять в **журнале запусков**.

:::image type="content" source="./media/custom-route-alert-portal/trigger.png" alt-text="Добавить учетную запись службы автоматизации":::

## <a name="next-steps"></a>Дальнейшие шаги

Дополнительные сведения о настройке рабочего процесса см. в разделе [Azure Logic Apps](../logic-apps/logic-apps-overview.md).