---
title: Настройка решения в пользовательском интерфейсе для удаленного мониторинга в Azure | Документация Майкрософт
description: Статья содержит сведения о способе доступа к исходному коду пользовательского интерфейса акселератора решений для удаленного мониторинга, а также о некоторых настройках.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 11/09/2018
ms.topic: conceptual
ms.openlocfilehash: dc2b38f8e8065b8d8763365bf0cbad56ae00cd4b
ms.sourcegitcommit: ba035bfe9fab85dd1e6134a98af1ad7cf6891033
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/01/2019
ms.locfileid: "55565434"
---
# <a name="customize-the-remote-monitoring-solution-accelerator"></a>Настройка акселератора решений для удаленного мониторинга

Статья содержит сведения о способе доступа к исходному коду и настройке пользовательского интерфейса акселератора решений для удаленного мониторинга.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prepare-a-local-development-environment-for-the-ui"></a>Подготовка локальной среды разработки для пользовательского интерфейса

Код пользовательского интерфейса акселератора решений для удаленного мониторинга реализуется с помощью платформы React.js. Исходный код можно найти в репозитории GitHub [azure-iot-pcs-remote-monitoring-webui](https://github.com/Azure/azure-iot-pcs-remote-monitoring-webui).

Чтобы внести изменения в пользовательский интерфейс, его копию можно запустить локально. Чтобы выполнить действия, например, чтобы извлечь данные телеметрии, локальную копию необходимо подключить к развернутому экземпляру решения.

Ниже описан процесс настройки локальной среды для развертывания пользовательского интерфейса.

1. Разверните **базовый** экземпляр акселератора решений с помощью CLI **PCS**. Запишите имя развертывания и учетные данные, указанные для виртуальной машины. Дополнительные сведения см. в статье [Развертывание предварительно настроенного решения для удаленного мониторинга с помощью интерфейса командной строки](iot-accelerators-remote-monitoring-deploy-cli.md).

1. Включите доступ по протоколу SSH к виртуальной машине, на которой размещаются микрослужбы в вашем решении с помощью портала Azure или Azure Cloud Shell. Например: 

    ```azurecli-interactive
    az network nsg rule update --name SSH --nsg-name {your solution name}-nsg --resource-group {your solution name} --access Allow
    ```

    Включать доступ по протоколу SSH нужно только во время тестирования и разработки. Если доступ по протоколу SSH включен, [его нужно отключить после использования](../security/azure-security-network-security-best-practices.md#disable-rdpssh-access-to-virtual-machines).

1. Используя портал Azure или Azure Cloud Shell, найдите имя и общедоступный IP-адрес виртуальной машины. Например: 

    ```azurecli-interactive
    az resource list --resource-group {your solution name} -o table
    az vm list-ip-addresses --name {your vm name from previous command} --resource-group {your solution name} -o table
    ```

1. Используйте SSH для подключения к виртуальной машине. Используйте IP-адрес из предыдущего шага и учетные данные, указанные при запуске **PCS** для развертывания решения. В Azure Cloud Shell доступна команда `ssh`.

1. Чтобы разрешить локальному пользовательскому интерфейсу установить подключение, выполните следующие команды в оболочке Bash на виртуальной машине.

    ```sh
    cd /app
    sudo ./start.sh --unsafe
    ```

1. После завершения команды и запуска веб-сайта можно отключиться от виртуальной машины.

1. В локальной копии репозитория [azure-iot-pcs-remote-monitoring-webui](https://github.com/Azure/azure-iot-pcs-remote-monitoring-webui) измените **.ENV**-файл, добавив URL-адрес развернутого решения.

    ```config
    NODE_PATH = src/
    REACT_APP_BASE_SERVICE_URL=https://{your solution name}.azurewebsites.net/
    ```

1. В командной строке перейдите к локальной копии папки `azure-iot-pcs-remote-monitoring-webui`.

1. Чтобы установить требуемые библиотеки и запустить пользовательский интерфейс локально, выполните следующие команды.

    ```cmd/sh
    npm install
    npm start
    ```

1. Предыдущая команда запускает пользовательский интерфейс локально по адресу http://localhost:3000/dashboard. Вы можете изменить код во время работы сайта.Он будет обновляться динамически.

## <a name="customize-the-layout"></a>Настройка макета

Каждая страница в решении для удаленного мониторинга состоит из набора элементов управления, которые называются *панелями* в исходном коде. Страница **Панель мониторинга** состоит из пяти панелей: "Обзор", "Карта", "Сигналы", "Телеметрия" и "Аналитика". Исходный код, определяющий каждую страницу и ее панели, находится в репозитории GitHub [pcs-remote-monitoring-webui](https://github.com/Azure/pcs-remote-monitoring-webui). Например, код, определяющий страницу **Панель мониторинга**, ее макет и панели на странице, расположен в папке [src/components/pages/dashboard](https://github.com/Azure/pcs-remote-monitoring-webui/tree/master/src/components/pages/dashboard).

Так как панели управляют собственным макетом и размером, вы можете без труда изменить макет страницы. Внесите следующие изменения в элемент **PageContent** в файле `src/components/pages/dashboard/dashboard.js`.

* Поменяйте местами панели "Карта" и "Телеметрия".
* Измените относительную ширину панелей карты и аналитики.

```javascript
<PageContent className="dashboard-container">
  <Grid>
    <Cell className="col-1 devices-overview-cell">
      <OverviewPanel
        activeDeviceGroup={activeDeviceGroup}
        openWarningCount={openWarningCount}
        openCriticalCount={openCriticalCount}
        onlineDeviceCount={onlineDeviceCount}
        offlineDeviceCount={offlineDeviceCount}
        isPending={analyticsIsPending || devicesIsPending}
        error={deviceGroupError || devicesError || analyticsError}
        t={t} />
    </Cell>
    <Cell className="col-6">
      <TelemetryPanel
        timeSeriesExplorerUrl={timeSeriesParamUrl}
        telemetry={telemetry}
        isPending={telemetryIsPending}
        lastRefreshed={lastRefreshed}
        error={deviceGroupError || telemetryError}
        theme={theme}
        colors={chartColorObjects}
        t={t} />
    </Cell>
    <Cell className="col-3">
      <AlertsPanel
        alerts={currentActiveAlertsWithName}
        isPending={analyticsIsPending || rulesIsPending}
        error={rulesError || analyticsError}
        t={t}
        deviceGroups={deviceGroups} />
    </Cell>
    <Cell className="col-4">
      <PanelErrorBoundary msg={t('dashboard.panels.map.runtimeError')}>
        <MapPanel
          analyticsVersion={analyticsVersion}
          azureMapsKey={azureMapsKey}
          devices={devices}
          devicesInAlert={devicesInAlert}
          mapKeyIsPending={azureMapsKeyIsPending}
          isPending={devicesIsPending || analyticsIsPending}
          error={azureMapsKeyError || devicesError || analyticsError}
          t={t} />
      </PanelErrorBoundary>
    </Cell>
    <Cell className="col-6">
      <AnalyticsPanel
        timeSeriesExplorerUrl={timeSeriesParamUrl}
        topAlerts={topAlertsWithName}
        alertsPerDeviceId={alertsPerDeviceType}
        criticalAlertsChange={criticalAlertsChange}
        isPending={analyticsIsPending || rulesIsPending || devicesIsPending}
        error={devicesError || rulesError || analyticsError}
        theme={theme}
        colors={chartColorObjects}
        t={t} />
    </Cell>
    {
      Config.showWalkthroughExamples &&
      <Cell className="col-4">
        <ExamplePanel t={t} />
      </Cell>
    }
  </Grid>
</PageContent>
```

![Изменение макета панели](./media/iot-accelerators-remote-monitoring-customize/layout.png)

Вы также можете добавить несколько экземпляров одной панели или нескольких версий, если [дублируете и настроите панель](#duplicate-and-customize-an-existing-control). В следующем примере показано, как добавить два экземпляра панели телеметрии. Чтобы выполнить изменения, отредактируйте файл `src/components/pages/dashboard/dashboard.js`.

```javascript
<PageContent className="dashboard-container">
  <Grid>
    <Cell className="col-1 devices-overview-cell">
      <OverviewPanel
        activeDeviceGroup={activeDeviceGroup}
        openWarningCount={openWarningCount}
        openCriticalCount={openCriticalCount}
        onlineDeviceCount={onlineDeviceCount}
        offlineDeviceCount={offlineDeviceCount}
        isPending={analyticsIsPending || devicesIsPending}
        error={deviceGroupError || devicesError || analyticsError}
        t={t} />
    </Cell>
    <Cell className="col-3">
      <TelemetryPanel
        timeSeriesExplorerUrl={timeSeriesParamUrl}
        telemetry={telemetry}
        isPending={telemetryIsPending}
        lastRefreshed={lastRefreshed}
        error={deviceGroupError || telemetryError}
        theme={theme}
        colors={chartColorObjects}
        t={t} />
    </Cell>
    <Cell className="col-3">
      <TelemetryPanel
        timeSeriesExplorerUrl={timeSeriesParamUrl}
        telemetry={telemetry}
        isPending={telemetryIsPending}
        lastRefreshed={lastRefreshed}
        error={deviceGroupError || telemetryError}
        theme={theme}
        colors={chartColorObjects}
        t={t} />
    </Cell>
    <Cell className="col-3">
      <AlertsPanel
        alerts={currentActiveAlertsWithName}
        isPending={analyticsIsPending || rulesIsPending}
        error={rulesError || analyticsError}
        t={t}
        deviceGroups={deviceGroups} />
    </Cell>
    <Cell className="col-4">
      <PanelErrorBoundary msg={t('dashboard.panels.map.runtimeError')}>
        <MapPanel
          analyticsVersion={analyticsVersion}
          azureMapsKey={azureMapsKey}
          devices={devices}
          devicesInAlert={devicesInAlert}
          mapKeyIsPending={azureMapsKeyIsPending}
          isPending={devicesIsPending || analyticsIsPending}
          error={azureMapsKeyError || devicesError || analyticsError}
          t={t} />
      </PanelErrorBoundary>
    </Cell>
    <Cell className="col-6">
      <AnalyticsPanel
        timeSeriesExplorerUrl={timeSeriesParamUrl}
        topAlerts={topAlertsWithName}
        alertsPerDeviceId={alertsPerDeviceType}
        criticalAlertsChange={criticalAlertsChange}
        isPending={analyticsIsPending || rulesIsPending || devicesIsPending}
        error={devicesError || rulesError || analyticsError}
        theme={theme}
        colors={chartColorObjects}
        t={t} />
    </Cell>
    {
      Config.showWalkthroughExamples &&
      <Cell className="col-4">
        <ExamplePanel t={t} />
      </Cell>
    }
  </Grid>
</PageContent>
```

Затем вы можете просмотреть разные данные телеметрии на каждой панели.

![Несколько панелей данных телеметрии](./media/iot-accelerators-remote-monitoring-customize/multiple-telemetry.png)

## <a name="duplicate-and-customize-an-existing-control"></a>Дублирование и настройка имеющихся элементов управления

Ниже показано, как дублировать имеющуюся панель, изменить ее, а затем использовать измененную версию. В качестве примера в инструкции используется панель **оповещений**.

1. В локальной копии репозитория создайте копию папки **alerts** в папке `src/components/pages/dashboard/panels`. Присвойте новой копии имя **cust_alerts**.

1. В файле **alertsPanel.js** папки **cust_alerts** измените имя класса на **CustAlertsPanel**:

    ```javascript
    export class CustAlertsPanel extends Component {
    ```

1. Добавьте следующую строку в файл `src/components/pages/dashboard/panels/index.js` :

    ```javascript
    export * from './cust_alerts';
    ```

1. Замените `alertsPanel` на `CustAlertsPanel` в файле `src/components/pages/dashboard/dashboard.js`:

    ```javascript
    import {
      OverviewPanel,
      CustAlertsPanel,
      TelemetryPanel,
      KpisPanel,
      MapPanel,
      transformTelemetryResponse,
      chartColors
    } from './panels';

    ...

    <Cell className="col-3">
      <CustAlertsPanel
        alerts={currentActivealertsWithName}
        isPending={kpisIsPending || rulesIsPending}
        error={rulesError || kpisError}
        t={t} />
    </Cell>
    ```

Вы заменили исходную панель **оповещения** копией **CustAlerts**. Эта копия идентична оригиналу. Теперь можно изменить копию. Например, чтобы изменить порядок столбцов в панели **оповещений**, сделайте следующее:

1. Откройте файл `src/components/pages/dashboard/panels/cust_alerts/alertsPanel.js` .

1. Измените определения столбцов, как показано в следующем фрагменте кода:

    ```javascript
    this.columnDefs = [
      rulesColumnDefs.severity,
      {
        headerName: 'rules.grid.count',
        field: 'count'
      },
      {
        ...rulesColumnDefs.ruleName,
        minWidth: 200
      },
      rulesColumnDefs.explore
    ];
    ```

На следующем рисунке показана новая версия панели **оповещений**:

![Обновленная панель оповещений](./media/iot-accelerators-remote-monitoring-customize/reorder-columns.png)

## <a name="customize-the-telemetry-chart"></a>Настройка диаграммы телеметрии

Файлы в папке `src/components/pages/dashboard/panels/telemtry` определяют диаграмму телеметрии на странице **Панель мониторинга**. Пользовательский интерфейс извлекает данные телеметрии из серверной части решения в файле `src/services/telemetryService.js`. Ниже показано, как изменить период времени, отображаемый на диаграмме телеметрии, с 15 до 5 минут:

1. В файле `src/services/telemetryService.js` найдите функцию **getTelemetryByDeviceIdP15M**. Скопируйте функцию и измените ее копию следующим образом:

    ```javascript
    static getTelemetryByDeviceIdP5M(devices = []) {
      return TelemetryService.getTelemetryByMessages({
        from: 'NOW-PT5M',
        to: 'NOW',
        order: 'desc',
        devices
      });
    }
    ```

1. Чтобы использовать эту функцию для заполнения диаграммы телеметрии, откройте файл `src/components/pages/dashboard/dashboard.js`. Найдите строку, которая инициализирует поток телеметрии, и измените ее следующим образом:

    ```javascript
    const getTelemetryStream = ({ deviceIds = [] }) => TelemetryService.getTelemetryByDeviceIdP5M(deviceIds)
    ```

Диаграмма телеметрии отображает данные телеметрии за 5 минут:

![Диаграмма телеметрии с данными за один день](./media/iot-accelerators-remote-monitoring-customize/telemetry-period.png)

## <a name="add-a-new-kpi"></a>Добавление нового ключевого показателя эффективности

Страница **Панель мониторинга** отображает ключевые показатели эффективности на панели **аналитики**. Эти показатели вычисляются в файле `src/components/pages/dashboard/dashboard.js`. Они преобразовываются для просмотра с помощью файла `src/components/pages/dashboard/panels/analytics/analyticsPanel.js`. Ниже показано, как рассчитать и преобразовать для просмотра новое значение ключевого показателя эффективности на странице **Панель мониторинга**. Ниже приведен пример того, как добавить новое процентное изменение в ключевом показателе эффективности предупреждающего оповещения.

1. Откройте файл `src/components/pages/dashboard/dashboard.js` . Измените объект **initialState**, чтобы он включал свойство **warningAlertsChange**, как показано ниже.

    ```javascript
    const initialState = {
      ...

      // Analytics data
      analyticsVersion: 0,
      currentActiveAlerts: [],
      topAlerts: [],
      alertsPerDeviceId: {},
      criticalAlertsChange: 0,
      warningAlertsChange: 0,
      analyticsIsPending: true,
      analyticsError: null

      ...
    };
    ```

1. Измените объект **currentAlertsStats**, чтобы он включал свойство **totalWarningCount**:

    ```javascript
    return {
      openWarningCount: (acc.openWarningCount || 0) + (isWarning && isOpen ? 1 : 0),
      openCriticalCount: (acc.openCriticalCount || 0) + (isCritical && isOpen ? 1 : 0),
      totalWarningCount: (acc.totalWarningCount || 0) + (isWarning ? 1 : 0),
      totalCriticalCount: (acc.totalCriticalCount || 0) + (isCritical ? 1 : 0),
      alertsPerDeviceId: updatedAlertsPerDeviceId
    };
    ```

1. Вычислите новый ключевой показатель эффективности. Найти вычисления по количеству критических оповещений. Дублируйте код и измените копию следующим образом:

    ```javascript
    // ================== Warning Alerts Count - START
    const currentWarningAlerts = currentAlertsStats.totalWarningCount;
    const previousWarningAlerts = previousAlerts.reduce(
      (cnt, { severity }) => severity === Config.ruleSeverity.warning ? cnt + 1 : cnt,
      0
    );
    const warningAlertsChange = ((currentWarningAlerts - previousWarningAlerts) / currentWarningAlerts * 100).toFixed(2);
    // ================== Warning Alerts Count - END
    ```

1. Добавьте ключевой показатель эффективности **warningAlertsChange** в поток данных КПЭ.

    ```javascript
    return ({
      analyticsIsPending: false,
      analyticsVersion: this.state.analyticsVersion + 1,

      // Analytics data
      currentActiveAlerts,
      topAlerts,
      criticalAlertsChange,
      warningAlertsChange,
      alertsPerDeviceId: currentAlertsStats.alertsPerDeviceId,

      ...
    });
    ```

1. Добавьте ключевой показатель эффективности **warningAlertsChange** в данные о состоянии, используемые для визуализации пользовательского интерфейса:

    ```javascript
    const {
      ...

      analyticsVersion,
      currentActiveAlerts,
      topAlerts,
      alertsPerDeviceId,
      criticalAlertsChange,
      warningAlertsChange,
      analyticsIsPending,
      analyticsError,

      ...
    } = this.state;
    ```

1. Обновите данные, передаваемые на панель ключевых показателей эффективности:

    ```javascript
    <AnalyticsPanel
      timeSeriesExplorerUrl={timeSeriesParamUrl}
      topAlerts={topAlertsWithName}
      alertsPerDeviceId={alertsPerDeviceType}
      criticalAlertsChange={criticalAlertsChange}
      warningAlertsChange={warningAlertsChange}
      isPending={analyticsIsPending || rulesIsPending || devicesIsPending}
      error={devicesError || rulesError || analyticsError}
      theme={theme}
      colors={chartColorObjects}
      t={t} />
    ```

Вы внесли нужные изменения в файл `src/components/pages/dashboard/dashboard.js`. Ниже описаны изменения, внесенные в файл `src/components/pages/dashboard/panels/analytics/analyticsPanel.js`, для отображения нового ключевого показателя эффективности.

1. Измените следующую строку кода для получения нового значения ключевого показателя эффективности следующим образом:

    ```javascript
    const { t, isPending, criticalAlertsChange, warningAlertsChange, alertsPerDeviceId, topAlerts, timeSeriesExplorerUrl, error } = this.props;
    ```

1. Измените разметку для отображения нового значения ключевого показателя эффективности следующим образом:

    ```javascript
    <div className="analytics-cell">
      <div className="analytics-header">{t('dashboard.panels.analytics.criticalAlerts')}</div>
      <div className="critical-alerts">
        {
          !showOverlay &&
            <div className="analytics-percentage-container">
              <div className="analytics-value">{ !isNaN(criticalAlertsChange) ? criticalAlertsChange : 0 }</div>
              <div className="analytics-percentage-sign">%</div>
            </div>
        }
      </div>
      <div className="critical-alerts">
        {
          !showOverlay &&
            <div className="analytics-percentage-container">
              <div className="analytics-value">{ !isNaN(warningAlertsChange) ? warningAlertsChange : 0 }</div>
              <div className="analytics-percentage-sign">%</div>
            </div>
        }
      </div>
    </div>
    ```

На странице **Панель мониторинга** теперь отображается новое значение ключевого показателя эффективности.

![Ключевой показатель эффективности предупреждения](./media/iot-accelerators-remote-monitoring-customize/new-kpi.png)

## <a name="customize-the-map"></a>Настройка карты

Дополнительные сведения о компонентах карты в решении см. на странице GitHub о [настройке карты](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide#upgrade-map-key-to-see-devices-on-a-dynamic-map).

<!--
### Connect an external visualization tool

See the [Connect an external visualization tool](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/) page in GitHub for details of how to connect an external visualization tool.

-->

## <a name="other-customization-options"></a>Другие возможности настройки

Для дополнительного изменения уровня представления и визуализации в решении для удаленного мониторинга можно изменить код. Соответствующие репозитории GitHub:

* [Микрослужба конфигурации для решений Интернета вещей Azure (.NET)](https://github.com/Azure/remote-monitoring-services-dotnet/tree/master/config)
* [Микрослужба конфигурации для решений Интернета вещей Azure (Java)](https://github.com/Azure/remote-monitoring-services-java/tree/master/config)
* [Веб-интерфейс для удаленного мониторинга Интернета вещей Azure PCS](https://github.com/Azure/pcs-remote-monitoring-webui)

## <a name="next-steps"></a>Дополнительная информация

Из статьи вы узнали о ресурсах, которые могут помочь вам при настройке пользовательского веб-интерфейса в акселераторе решений для удаленного мониторинга. Чтобы узнать больше о настройке пользовательского интерфейса, обратитесь к следующим статьям:

* [Добавление пользовательской страницы в веб-интерфейс акселератора решений для удаленного мониторинга](iot-accelerators-remote-monitoring-customize-page.md)
* [Добавление пользовательской службы в веб-интерфейс акселератора решений для удаленного мониторинга](iot-accelerators-remote-monitoring-customize-service.md)
* [Добавление пользовательской сетки в веб-интерфейс акселератора решений для удаленного мониторинга](iot-accelerators-remote-monitoring-customize-grid.md)
* [Добавление пользовательского всплывающего элемента в веб-интерфейс акселератора решений для удаленного мониторинга](iot-accelerators-remote-monitoring-customize-flyout.md)
* [Добавление пользовательской панели на панель мониторинга в веб-интерфейсе акселератора решений для удаленного мониторинга](iot-accelerators-remote-monitoring-customize-panel.md)

Более подробные сведения об акселераторе решений для удаленного мониторинга см. в статье [Архитектура предварительно настроенного решения удаленного мониторинга](iot-accelerators-remote-monitoring-sample-walkthrough.md).

Дополнительные сведения о настройке решения для удаленного мониторинга микрослужб см. в статье [Настройка и повторное развертывание микрослужбы](iot-accelerators-microservices-example.md).
<!-- Next tutorials in the sequence -->
