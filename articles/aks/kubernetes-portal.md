---
title: Доступ к ресурсам Kubernetes из портал Azure (Предварительная версия)
description: Узнайте, как взаимодействовать с ресурсами Kubernetes для управления кластером службы Kubernetes Azure (AKS) из портал Azure.
services: container-service
author: laurenhughes
ms.topic: article
ms.date: 08/11/2020
ms.author: lahugh
ms.openlocfilehash: 4a0acf284475f3c9119f3b9d012debad656b1faa
ms.sourcegitcommit: d18a59b2efff67934650f6ad3a2e1fe9f8269f21
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/20/2020
ms.locfileid: "88661356"
---
# <a name="access-kubernetes-resources-from-the-azure-portal-preview"></a>Доступ к ресурсам Kubernetes из портал Azure (Предварительная версия)

Портал Azure включает средство просмотра ресурсов Kubernetes (Предварительная версия) для быстрого доступа к ресурсам Kubernetes в кластере службы Kubernetes Azure (AKS). Просмотр ресурсов Kubernetes из портал Azure сокращает переключение контекста между портал Azure и `kubectl` программой командной строки, что упрощает процесс просмотра и редактирования ресурсов Kubernetes. В настоящее время средство просмотра ресурсов включает несколько типов ресурсов, таких как развертывания, модули Pod и наборы реплик.

Представление ресурсов Kubernetes из портал Azure заменяет [надстройку панели мониторинга AKS][kubernetes-dashboard], которая задается для устаревания.

>[!NOTE]
>В настоящее время капабилти не поддерживается в [частных кластерах службы Kubernetes Azure](https://docs.microsoft.com/azure/aks/private-clusters).

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

## <a name="prerequisites"></a>Предварительные требования

Для просмотра ресурсов Kubernetes в портал Azure требуется кластер AKS. Поддерживается любой кластер, но при использовании интеграции Azure Active Directory (Azure AD) кластер должен использовать [интеграцию Azure AD, управляемую AKS][aks-managed-aad]. Если кластер использует устаревшую версию Azure AD, вы можете обновить кластер на портале или с помощью [Azure CLI][cli-aad-upgrade].

## <a name="view-kubernetes-resources"></a>Просмотр ресурсов Kubernetes

Чтобы просмотреть ресурсы Kubernetes, перейдите к кластеру AKS в портал Azure. Панель навигации слева используется для доступа к ресурсам. К ресурсам относятся следующие компоненты:

- **Пространства имен** отображает пространства имен кластера. Фильтр в верхней части списка пространств имен предоставляет быстрый способ фильтрации и просмотра ресурсов пространства имен.
- **Рабочие нагрузки** отображает сведения о развертываниях, Pod, наборах реплик и наборах управляющих программ, развернутых в кластере. На снимке экрана ниже показаны системные модули по умолчанию в примере кластера AKS.
- **Службы и передает** показывают все ресурсы службы и входящих ресурсов вашего кластера.

:::image type="content" source="media/kubernetes-portal/workloads.png" alt-text="Сведения о Pod Kubernetes, отображаемые в портал Azure." lightbox="media/kubernetes-portal/workloads.png":::

### <a name="deploy-an-application"></a>Развертывание приложения

В этом примере мы будем использовать наш пример кластера AKS для развертывания приложения Azure для голосования из [краткого руководства по AKS][portal-quickstart].

1. Выберите **Добавить** из любого представления ресурсов (пространство имен, рабочие нагрузки, службы и передает).
1. Вставьте YAML для приложения Azure для голосования из [краткого руководства по AKS][portal-quickstart].
1. Выберите **Добавить** в нижней части редактора YAML, чтобы развернуть приложение. 

После добавления файла YAML в средстве просмотра ресурсов отобразятся обе созданные службы Kubernetes: внутренняя служба (Azure-голосовать-Back) и внешняя служба (Azure-голосовать-Front) для доступа к приложению Azure для голосования. Внешняя служба включает связанный внешний IP-адрес, что позволяет легко просматривать приложение в браузере.

:::image type="content" source="media/kubernetes-portal/portal-services.png" alt-text="Сведения о приложении Azure для голосования, отображаемые в портал Azure." lightbox="media/kubernetes-portal/portal-services.png":::

### <a name="monitor-deployment-insights"></a>Мониторинг аналитики развертывания

Кластеры AKS с включенным [Azure Monitor для контейнеров][enable-monitor] позволяют быстро просматривать аналитические сведения о развертывании. В представлении ресурсов Kubernetes пользователи могут просматривать состояние отдельных развертываний, включая использование ЦП и памяти, а также переход на Azure Monitor для получения более подробных сведений. Ниже приведен пример аналитической информации по развертыванию из примера кластера AKS.

:::image type="content" source="media/kubernetes-portal/deployment-insights.png" alt-text="Аналитика развертывания, отображаемая в портал Azure." lightbox="media/kubernetes-portal/deployment-insights.png":::

## <a name="edit-yaml"></a>Изменить YAML

Представление ресурсов Kubernetes также включает редактор YAML. Встроенный редактор YAML означает, что вы можете обновлять или создавать службы и развертывания на портале и немедленно применять изменения.

:::image type="content" source="media/kubernetes-portal/service-editor.png" alt-text="Редактор YAML для службы Kubernetes, отображаемой в портал Azure.":::

После изменения YAML примените изменения, выбрав " **проверить и сохранить**", подтверждая изменения, а затем сохранив их еще раз.

>[!WARNING]
> Выполнять прямые изменения рабочей среды через пользовательский интерфейс или CLI не рекомендуется, вы должны использовать [рекомендации по непрерывной интеграции (CI) и непрерывному развертыванию (CD)](kubernetes-action.md). Возможности управления Kubernetes портала Azure и редактор YAML созданы для обучения и создания новых развертываний в параметрах разработки и тестирования.

## <a name="troubleshooting"></a>Устранение неполадок

В этом разделе рассматриваются распространенные проблемы и действия по устранению неполадок.

### <a name="unauthorized-access"></a>Несанкционированный доступ.

Для доступа к ресурсам Kubernetes необходимо иметь доступ к кластеру AKS, API Kubernetes и объектам Kubernetes. Убедитесь, что вы являетесь администратором кластера или пользователем с соответствующими разрешениями для доступа к кластеру AKS. Дополнительные сведения о безопасности кластера см. в разделе [параметры доступа и удостоверений для AKS][concepts-identity].

### <a name="enable-resource-view"></a>Включить представление ресурсов

Для существующих кластеров может потребоваться включить представление ресурсов Kubernetes. Чтобы включить представление ресурсов, следуйте инструкциям на портале для кластера.

:::image type="content" source="media/kubernetes-portal/enable-resource-view.png" alt-text="Портал Azure сообщение, чтобы включить представление ресурсов Kubernetes." lightbox="media/kubernetes-portal/enable-resource-view.png":::

## <a name="next-steps"></a>Дальнейшие действия

В этой статье показано, как получить доступ к ресурсам Kubernetes для кластера AKS. Дополнительные сведения о ресурсах кластера и файлах YAML, доступ к которым осуществляется с помощью средства просмотра ресурсов Kubernetes, см. в разделе [развертывания и манифесты YAML][deployments] .

<!-- LINKS - internal -->
[kubernetes-dashboard]: kubernetes-dashboard.md
[concepts-identity]: concepts-identity.md
[portal-quickstart]: kubernetes-walkthrough-portal.md#run-the-application
[deployments]: concepts-clusters-workloads.md#deployments-and-yaml-manifests
[aks-managed-aad]: managed-aad.md
[cli-aad-upgrade]: managed-aad.md#upgrading-to-aks-managed-azure-ad-integration
[enable-monitor]: ../azure-monitor/insights/container-insights-enable-existing-clusters.md
