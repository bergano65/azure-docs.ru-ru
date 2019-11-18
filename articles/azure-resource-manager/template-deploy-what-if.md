---
title: Шаблоны развертывания что если (Предварительная версия)
description: Прежде чем развертывать шаблон Azure Resource Manager, определите, какие изменения будут выполнены для ресурсов.
author: mumian
ms.topic: conceptual
ms.date: 11/12/2019
ms.author: jgao
ms.openlocfilehash: 117215e7c41ad7f354c9e76f764e9af1f50b74c1
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/17/2019
ms.locfileid: "74149226"
---
# <a name="resource-manager-template-deployment-what-if-operation-preview"></a>Операция развертывания шаблона диспетчер ресурсов что если (Предварительная версия)

Перед развертыванием шаблона может потребоваться предварительный просмотр изменений, которые будут выполняться. Azure Resource Manager предоставляет операцию "что если", которая позволяет увидеть, как ресурсы изменятся при развертывании шаблона. Операция "что если" не вносит изменения в существующие ресурсы. Вместо этого он прогнозирует изменения, если заданный шаблон развернут.

> [!NOTE]
> Операция "что если" сейчас находится в предварительной версии. Чтобы использовать его, необходимо [зарегистрироваться для использования предварительной версии](https://aka.ms/armtemplatepreviews). В качестве предварительной версии результаты могут показывать, что ресурс изменится, когда фактическое изменение не произойдет. Мы работаем над сокращением этих проблем, но нам нужна ваша помощь. Сообщите эти проблемы по адресу [https://aka.ms/armwhatifissues](https://aka.ms/armwhatifissues).

Операцию "что если" можно выполнить с помощью команды `New-AzDeploymentWhatIf` PowerShell или операций [развертывания What If](/rest/api/resources/deployments/whatif) .

В PowerShell выходные данные выглядят следующим образом:

![диспетчер ресурсов развертывания шаблона что такое операция фуллресаурцепайлоад и изменение типов](./media/template-deploy-what-if/resource-manager-deployment-whatif-change-types.png)

## <a name="change-types"></a>Изменение типов

Операция "что если" перечисляет шесть различных типов изменений:

- **Создать**: ресурс в настоящее время не существует, но определен в шаблоне. Ресурс будет создан.

- **Delete**: этот тип изменения применяется только при использовании [полного режима](deployment-modes.md) развертывания. Ресурс существует, но не определен в шаблоне. В полном режиме ресурс будет удален. В этот тип изменений включаются только ресурсы, [поддерживающие удаление полного режима](complete-mode-deletion.md) .

- **Ignore**: ресурс существует, но не определен в шаблоне. Ресурс не будет развернут или изменен.

- **Без изменений**: ресурс существует и определен в шаблоне. Ресурс будет повторно развернут, но свойства ресурса не изменятся. Этот тип изменения возвращается, если [ресултформат](#result-format) имеет значение `FullResourcePayloads`, которое является значением по умолчанию.

- **Modify**: ресурс существует и определен в шаблоне. Ресурс будет повторно развернут, и свойства ресурса будут изменены. Этот тип изменения возвращается, если [ресултформат](#result-format) имеет значение `FullResourcePayloads`, которое является значением по умолчанию.

- **Deploy**: ресурс существует и определен в шаблоне. Ресурс будет повторно развернут. Свойства ресурса могут изменяться или не изменяться. Операция возвращает этот тип изменений, если у него недостаточно сведений, чтобы определить, изменяются ли какие либо свойства. Это условие отображается только в том случае, если [ресултформат](#result-format) имеет значение `ResourceIdOnly`.

## <a name="deployment-scope"></a>Область развертывания

Операцию "что если" можно использовать для развертываний на уровне подписки или группы ресурсов. Область развертывания задается параметром `-ScopeType`. Допустимые значения: `Subscription` и `ResourceGroup`. В этой статье описываются развертывания групп ресурсов.

Дополнительные сведения о развертывании на уровне подписки см. в статье [Создание групп ресурсов и ресурсов на уровне подписки](deploy-to-subscription.md#).

## <a name="result-format"></a>Формат результата

Можно контролировать уровень детализации, возвращаемый о прогнозируемых изменениях. Задайте для параметра `ResultFormat` значение `FullResourcePayloads`, чтобы получить список ресурсов, которые будут изменены, и сведения о свойствах, которые будут изменены. Задайте для параметра `ResultFormat` значение `ResourceIdOnly`, чтобы получить список ресурсов, которые будут изменены. По умолчанию используется значение `FullResourcePayloads`.  

На следующих снимках экрана показаны два разных формата выходных данных:

- Полные полезные данные ресурсов

    ![диспетчер ресурсов выходным данным о развертывании шаблона действие фуллресаурцепайлоадс](./media/template-deploy-what-if/resource-manager-deployment-whatif-output-fullresourcepayload.png)

- Только идентификатор ресурса

    ![диспетчер ресурсов выходным данным о развертывании шаблона действие ресаурцеидонли](./media/template-deploy-what-if/resource-manager-deployment-whatif-output-resourceidonly.png)

## <a name="run-what-if-operation"></a>Выполнить операцию "что если"

### <a name="set-up-environment"></a>Настройка среды

Чтобы узнать, как работает, давайте выполним некоторые тесты. Сначала разверните шаблон в шаблонах быстрого запуска [Azure, который создает учетную запись хранения](https://github.com/Azure/azure-quickstart-templates/blob/master/101-storage-account-create/azuredeploy.json). Тип учетной записи хранения по умолчанию — `Standard_LRS`. Эта учетная запись хранения будет использоваться для тестирования того, как сообщения о внесении изменений сообщаются в.

```azurepowershell-interactive
New-AzResourceGroup `
  -Name ExampleGroup `
  -Location centralus
New-AzResourceGroupDeployment `
  -ResourceGroupName ExampleGroup `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json"
```

### <a name="test-modification"></a>Изменение теста

После завершения развертывания вы можете протестировать операцию "что если". Выполните команду "что если", но измените тип учетной записи хранения на `Standard_GRS`.

```azurepowershell-interactive
New-AzDeploymentWhatIf `
  -ScopeType ResourceGroup `
  -ResourceGroupName ExampleGroup `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json" `
  -storageAccountType Standard_GRS
```

Выходные данные "что если" похожи на:

![Результат операции развертывания шаблона диспетчер ресурсов что-if](./media/template-deploy-what-if/resource-manager-deployment-whatif-output.png)

Обратите внимание, что в верхней части выходных данных определены цвета, указывающие тип изменений.

В нижней части выходных данных отобразится имя SKU (тип учетной записи хранения) с **Standard_LRS** на **Standard_GRS**.

### <a name="test-deletion"></a>Удаление теста

Операция "что если" поддерживает использование [режима развертывания](deployment-modes.md). Если задано значение полного режима, ресурсы, отсутствующие в шаблоне, удаляются. В следующем примере выполняется развертывание [шаблона, не имеющего ресурсов, определенных](https://github.com/Azure/azure-docs-json-samples/blob/master/empty-template/azuredeploy.json) в полном режиме.

```azurepowershell-interactive
New-AzDeploymentWhatIf `
  -ScopeType ResourceGroup `
  -ResourceGroupName ExampleGroup `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/empty-template/azuredeploy.json" `
  -Mode Complete
```

Так как в шаблоне не определены ресурсы, а для режима развертывания задано значение "завершено", учетная запись хранения будет удалена.

![Диспетчер ресурсов развертывание шаблона "что если" выходное режим развертывания операции завершен](./media/template-deploy-what-if/resource-manager-deployment-whatif-output-mode-complete.png)

Важно помнить, что, если не делает фактических изменений. Учетная запись хранения по-прежнему существует в группе ресурсов.

## <a name="next-steps"></a>Дополнительная информация

- Если вы заметили неверные результаты в предварительной версии, то сообщите о проблемах по адресу [https://aka.ms/armwhatifissues](https://aka.ms/armwhatifissues).
- Сведения о развертывании шаблонов с помощью Azure PowerShell см. в статье [развертывание ресурсов с помощью шаблонов диспетчер ресурсов и Azure PowerShell](resource-group-template-deploy.md).
- Сведения о развертывании шаблонов с помощью RESTFUL см. в статье [развертывание ресурсов с помощью шаблонов диспетчер ресурсов и диспетчер ресурсов REST API](resource-group-template-deploy-rest.md).
- Сведения о откате к успешному развертыванию при возникновении ошибки см. в разделе [откат при ошибке для успешного развертывания](rollback-on-error.md).
