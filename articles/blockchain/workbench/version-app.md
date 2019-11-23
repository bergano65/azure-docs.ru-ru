---
title: Blockchain app versioning - Azure Blockchain Workbench
description: How to use application versions in Azure Blockchain Workbench Preview.
ms.date: 11/20/2019
ms.topic: article
ms.reviewer: brendal
ms.openlocfilehash: 45219790cf0cd064e0fcd456e262b2f93aa03ac9
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/22/2019
ms.locfileid: "74323912"
---
# <a name="azure-blockchain-workbench-preview-application-versioning"></a>Azure Blockchain Workbench Preview application versioning

You can create and use multiple versions of an Azure Blockchain Workbench Preview app. Если передано несколько версий одного и того же приложения, доступен журнал версий и пользователи могут выбирать версию, которую они хотят использовать.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Технические условия

* Развертывание Blockchain Workbench. Дополнительные сведения о развертывании Azure Blockchain Workbench см. в [этой статье](deploy.md).
* Развернутое блокчейн-приложение в Blockchain Workbench. Сведения см. в статье [Создание блокчейн-приложения в Azure Blockchain Workbench](create-app.md).

## <a name="add-an-app-version"></a>Добавление версии приложения

Чтобы добавить новую версию, передайте новые файлы конфигурации и смарт-контракта в Blockchain Workbench.

1. В веб-браузере перейдите по адресу Blockchain Workbench. Например, `https://{workbench URL}.azurewebsites.net/`. Сведения о том, как найти веб-адрес Blockchain Workbench, см. в разделе об [URL-адресе Blockchain Workbench](deploy.md#blockchain-workbench-web-url).
2. Войдите как [администратор Blockchain Workbench](manage-users.md#manage-blockchain-workbench-administrators).
3. Выберите блокчейн-приложение, для которого вы хотите обновить версию.
4. Выберите **Добавить версию**. Откроется панель **добавления версии**.
5. Выберите для отправки файлы конфигурации контракта и файлы кода контракта новой версии. Файл конфигурации пройдет автоматическую проверку. Исправьте любые ошибки проверки перед развертыванием приложения.
6. Выберите **Добавить версию**, чтобы добавить новую версию блокчейн-приложения.

    ![Добавление новой версии](media/version-app/add-version.png)

Развертывание блокчейн-приложения может занять несколько минут. Обновите страницу приложения после того, как завершится развертывание. После выбора приложения и нажатия кнопки **Журнал версий** отобразится журнал версий приложения.

> [!IMPORTANT]
> Предыдущие версии приложения отключены. Вы можете снова включить предыдущие версии по отдельности.
>
> Может потребоваться повторно добавить участников в роли приложения, если в них были внесены изменения в новой версии.

## <a name="using-app-versions"></a>Использование версий приложения

В Blockchain Workbench по умолчанию используется последняя включенная версия приложения. Если вы хотите использовать предыдущую версию приложения, необходимо сначала выбрать версию на странице приложения.

1. В разделе приложения Blockchain Workbench установите флажок возле приложения, что содержит контракт, который вы хотите использовать. Если включены предыдущие версии, доступна кнопка журнала версий.
2. Нажмите кнопку **Журнал версий**.
3. В области журнала версий выберите версию приложения, щелкнув ссылку в столбце *Дата изменения*.

    ![Выбор предыдущей версии](media/version-app/use-version.png)

    Вы можете создать новые контракты или выполнять действия с контрактами предыдущих версий. Версия приложения отображается после имени приложения. При использовании старой версии отображается соответствующее предупреждение.

## <a name="next-steps"></a>Дальнейшие действия

* [Устранение неполадок в Azure Blockchain Workbench](troubleshooting.md)
