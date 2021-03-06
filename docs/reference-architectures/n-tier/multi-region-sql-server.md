---
title: Высокодоступное N-уровневое приложение с поддержкой нескольких регионов
titleSuffix: Azure Reference Architectures
description: Разверните приложение на виртуальных машинах Azure в нескольких регионах, чтобы обеспечить высокий уровень доступности и устойчивость.
author: MikeWasson
ms.date: 07/19/2018
ms.topic: reference-architecture
ms.service: architecture-center
ms.subservice: reference-architecture
ms.custom: seodec18
ms.openlocfilehash: 9fce082a0e762e25981929f6fa8685033017f3eb
ms.sourcegitcommit: c053e6edb429299a0ad9b327888d596c48859d4a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/20/2019
ms.locfileid: "58245475"
---
# <a name="run-an-n-tier-application-in-multiple-azure-regions-for-high-availability"></a>Запуск n-уровневого приложения в нескольких регионах Azure для обеспечения высокой доступности

Эта эталонная архитектура демонстрирует набор проверенных рекомендаций по работе с n-уровневыми приложениями в нескольких регионах Azure, благодаря которым можно достигнуть высокой доступности и получить надежную архитектуру аварийного восстановления.

![Высокодоступная сетевая архитектура для n-уровневых приложений Azure](./images/multi-region-sql-server.png)

*Скачайте [файл Visio][visio-download] этой архитектуры.*

## <a name="architecture"></a>Архитектура

Эта архитектура создана на основе архитектуры, описанной в статье [N-уровневое приложение с SQL Server](n-tier-sql-server.md).

- **Основной и дополнительный регионы.** Чтобы достичь более высокого уровня доступности, используйте два региона Azure, один из которых является основным, а второй предназначен для отработки отказа.

- **Диспетчер трафика Azure.** [Диспетчер трафика][traffic-manager] направляет входящие запросы к одному из регионов. При обычной работе он направляет запросы в основной регион. Если этот регион становится недоступным, диспетчер трафика выполняет отработку отказа в дополнительный регион. Дополнительные сведения см. в разделе [Конфигурация диспетчера трафика](#traffic-manager-configuration).

- **Группы ресурсов.** Создайте отдельные [группы ресурсов][resource groups] для основного и вторичного регионов, а также для диспетчера трафика. Так вы получите возможность управлять каждым регионом как одной коллекцией ресурсов. Например, можно повторно развернуть один регион, не отключая другой. [Свяжите группы ресурсов][resource-group-links], чтобы запустить запрос на перечисление всех ресурсов приложения.

- **Виртуальные сети.** Создайте отдельные виртуальные сети для каждого региона. Убедитесь, что указанные пространства адресов не перекрываются.

- **Группа доступности AlwaysOn SQL Server.** При использовании SQL Server рекомендуется использовать [группы доступности SQL Always On][sql-always-on] для обеспечения высокого уровня доступности. Создайте одну группу доступности, которая включает экземпляры SQL Server в обоих регионах.

    > [!NOTE]
    > Кроме того, учитывайте [базу данных SQL Azure][azure-sql-db], которая предоставляет реляционную базу данных в качестве облачной службы. В базе данных SQL не нужно настраивать группу доступности или управлять отработкой отказа.
    >

- **VPN-шлюзы.** Создайте [VPN-шлюз][vpn-gateway] в каждой виртуальной сети и настройте [подключение "виртуальная сеть — виртуальная сеть"][vnet-to-vnet], чтобы разрешить трафик между двумя виртуальными сетями. Это необходимо для группы доступности AlwaysOn SQL.

## <a name="recommendations"></a>Рекомендации

Архитектура с несколькими регионами может обеспечить более высокий уровень доступности, чем развертывание в одном регионе. Если региональный сбой влияет на основной регион, можно использовать [диспетчер трафика][traffic-manager] для выполнения отработки отказа в дополнительный регион. Эта архитектура также помогает при сбое отдельной подсистемы или приложения.

Есть несколько общих подходов к достижению высокого уровня доступности в регионах:

- Шаблон "активный — пассивный" с "горячим" резервом. Трафик отправляется в один регион, в то время как другой ожидает в режиме "горячего" резерва. "Горячий" резерв применяется, когда виртуальные машины в дополнительном регионе выделены и выполняются постоянно.
- Шаблон "активный — пассивный" с "холодным" резервом. Трафик отправляется в один регион, в то время как другой ожидает в режиме "холодного" резерва. "Холодный" резерв предполагает, что виртуальные машины в дополнительном регионе выделяются, только когда они требуются для отработки отказа. Этот подход экономичнее, однако при сбое для выхода в динамический режим требуется больше времени.
- Шаблон "активный — активный". Оба региона активны, нагрузка запросов балансируется между ними. Если один регион отключается, он изымается из ротации.

В этой эталонной архитектуре уделяется внимание режиму "активный — пассивный" с "горячим" резервом, а также использованию диспетчера трафика для отработки отказа. Обратите внимание, что можно развернуть несколько виртуальных машин в качестве "горячего" резерва, а затем масштабировать их при необходимости.

### <a name="regional-pairing"></a>Региональные пары

Каждый регион Azure образует пару с другим регионом в пределах одной географической территории. В общем случае выбирайте регионы из одной региональной пары (например, восточная часть США 2, центральная часть США). Преимущества:

- в случае масштабного сбоя назначаются приоритеты восстановления по крайней мере одного региона из каждой пары;
- запланированные обновления системы Azure распространяются в парах регионов последовательно во избежание возможных простоев;
- пары находятся в пределах одной географической территории в соответствии с требованиями к местонахождению данных.

Убедитесь, что оба региона поддерживают все службы Azure, необходимые приложению (см. статью [Доступность продуктов по регионам][services-by-region]). Дополнительные сведения о парах регионов см. в статье [Непрерывность бизнес-процессов и аварийное восстановление в службах BizTalk: пары регионов Azure][regional-pairs].

### <a name="traffic-manager-configuration"></a>Конфигурация диспетчера трафика

При настройке диспетчера трафика необходимо учитывать следующее:

- **Маршрутизация.** Диспетчер трафика поддерживает несколько [алгоритмов маршрутизации][tm-routing]. Для сценария, описанного в этой статье, используется маршрутизация по *приоритету* (ранее называлась маршрутизацией *отработки отказа*). С помощью этой функции диспетчер трафика отправляет все запросы в основной регион, если дополнительный регион не станет недоступным. В этот момент он автоматически выполняет отработку отказа в дополнительный регион. Дополнительные сведения см. в статье [Настройка метода маршрутизации трафика по приоритету в диспетчере трафика][tm-configure-failover].
- **Проверка работоспособности.** Диспетчер трафика использует [проверку][tm-monitoring] HTTP (или HTTPS) для мониторинга доступности каждого региона. При этом проверяется код ответа HTTP 200 в заданном пути URL-адреса. Рекомендуется создать конечную точку, которая сообщает о работоспособности приложения, и использовать ее для проверки работоспособности. В противном случае при проверке может быть сообщено о работоспособной конечной точке, тогда как критические части приложения фактически не будут работать. См. дополнительные сведения о [шаблоне мониторинга конечной точки работоспособности][health-endpoint-monitoring-pattern].

При выполнении отработки отказа диспетчера трафика в течение определенного времени клиенты не могут связаться с приложением. Этот период зависит от следующих факторов:

- При проверке работоспособности определяется, что с основным регионом невозможно связаться.
- DNS-серверы должны обновить кэшированные записи DNS для IP-адресов, которые зависят от срока существования DNS. Срок существования по умолчанию — 300 секунд (5 минут), однако это значение можно настроить при создании профиля диспетчера трафика.

Дополнительные сведения см. в статье [Мониторинг конечных точек в диспетчере трафика][tm-monitoring].

При отработке отказа диспетчера трафика ее рекомендуется выполнять вручную, а не внедрять автоматическую отработку отказа. В противном случае могут возникнуть ситуации, в которых приложение будет переходить между регионами. Убедитесь, что все подсистемы приложения полностью работоспособны, и лишь затем выполните восстановление размещения.

Обратите внимание, что диспетчер трафика по умолчанию автоматически восстанавливает размещение. Чтобы избежать этого, вручную понизьте приоритет основного региона после отработки отказа. Например, предположим, что основной регион имеет приоритет 1, а дополнительные — приоритет 2. После отработки отказа задайте основному региону приоритет 3, чтобы избежать автоматического восстановления размещения. Когда будете готовы переключить приоритет обратно, измените приоритет до 1.

Следующая команда [Azure CLI][azure-cli] обновляет приоритет:

```azurecli
az network traffic-manager endpoint update --resource-group <resource-group> --profile-name <profile>
    --name <endpoint-name> --type azureEndpoints --priority 3
```

Другой подход заключается во временном отключении конечной точки, пока не появится возможность восстановить размещение:

```azurecli
az network traffic-manager endpoint update --resource-group <resource-group> --profile-name <profile>
    --name <endpoint-name> --type azureEndpoints --endpoint-status Disabled
```

В зависимости от причины отработки отказа может потребоваться повторное развертывание ресурсов в пределах региона. До восстановления размещения выполните тест готовности к работе. При этом проверяется следующее:

- правильная настройка виртуальных машин (все необходимое программное обеспечение установлено, IIS выполняются и т. д.);
- работоспособность подсистем приложения;
- функциональное тестирование (например, доступен ли уровень базы данных с веб-уровня).

### <a name="configure-sql-server-always-on-availability-groups"></a>Настройка группы доступности AlwaysOn SQL Server

До Windows Server 2016 для групп доступности SQL Server Always On требовался контроллер домена, а все узлы в группе доступности должны были находиться в одном домене Active Directory (AD).

Чтобы настроить группу доступности, сделайте следующее:

- Поместите как минимум два контроллера домена в каждом регионе.
- Предоставьте каждому контроллеру домена статический IP-адрес.
- Создайте подключение "виртуальная сеть — виртуальная сеть", чтобы включить обмен данными между виртуальными сетями.
- Для каждой виртуальной сети добавьте IP-адреса контроллеров доменов (из обоих регионов) в список DNS-серверов. Вы можете использовать следующую команду интерфейса командной строки. Дополнительные сведения см. в разделе [Изменение DNS-серверов][vnet-dns].

    ```azurecli
    az network vnet update --resource-group <resource-group> --name <vnet-name> --dns-servers "10.0.0.4,10.0.0.6,172.16.0.4,172.16.0.6"
    ```

- Создайте [отказоустойчивый кластер Windows Server][wsfc] (WSFC), включающий экземпляры SQL Server в обоих регионах.
- Создайте группу доступности Always On SQL Server, в которую включены экземпляры SQL Server в основном и дополнительном регионах. Дополнительные сведения о шагах см. в статье [Extending AlwaysOn Availability Group to Remote Azure Datacenter (PowerShell)](https://techcommunity.microsoft.com/t5/DataCAT/Extending-AlwaysOn-Availability-Group-to-Remote-Azure-Datacenter/ba-p/305217) (Расширение групп доступности Always On на удаленный центр обработки данных Azure (PowerShell)).

  - Поместите первичную реплику в основной регион.
  - Поместите одну или несколько вторичных реплик в основной регион. Настройте для них использование синхронного фиксирования с автоматическим переходом на другой ресурс.
  - Поместите одну или несколько вторичных реплик в дополнительный регион. Настройте для них использование *асинхронного* фиксирования для повышения производительности (иначе все транзакции T-SQL должны ожидать кругового перехода по сети до дополнительного региона).

    > [!NOTE]
    > Асинхронные реплики фиксирования не поддерживают автоматический переход на другой ресурс.

## <a name="availability-considerations"></a>Вопросы доступности

С помощью сложного n-уровневого приложения можно не реплицировать все приложение в дополнительном регионе. Вместо этого вы можете просто реплицировать критическую подсистему, необходимую для обеспечения непрерывной работы бизнеса.

Диспетчер трафика — это точка возможного сбоя в системе. Если происходит сбой диспетчера трафика, клиенты не смогут получить доступ к приложению во время простоя. Просмотрите [соглашение об уровне обслуживания диспетчера трафика][tm-sla] и подумайте, достаточно ли диспетчера трафика в соответствии с требованиями к высокой доступности в вашей организации. Если это не так, добавьте резервное решение для управления трафиком. Если в службе диспетчера трафика Azure произошел сбой, измените записи CNAME в службе доменных имен, чтобы они указывали на резервную службу управления трафиком. (Этот шаг нужно выполнить вручную, приложение будет отключено, пока изменения DNS не распространятся.)

В кластере SQL Server необходимо учитывать два сценария отработки отказа:

- Происходит сбой всех реплик базы данных SQL Server в основном регионе. Например, это может произойти при региональном сбое. В этом случае необходимо вручную переключить группу доступности, несмотря на то, что диспетчер трафика автоматически переключается на внешнем интерфейсе. Выполните шаги в статье [Perform a Forced Manual Failover of a SQL Server Availability Group](https://msdn.microsoft.com/library/ff877957.aspx) (Выполнение принудительного перехода на другой ресурс вручную для группы доступности (SQL Server)), в которой описано, как выполнять принудительный переход на другой ресурс с помощью SQL Server Management Studio, Transact-SQL или PowerShell в SQL Server 2016.

   > [!WARNING]
   > При принудительном переходе на другой ресурс есть риск потери данных. Когда основной регион будет восстановлен, сделайте моментальный снимок базы данных и используйте [tablediff], чтобы найти отличия.

- Диспетчер трафика выполняет отработку отказа в дополнительный регион, однако основная реплика базы данных SQL Server все еще доступна. Например, сбой внешнего уровня может не затронуть виртуальные машины SQL Server. В этом случае интернет-трафик направляется в дополнительный регион, а этот регион может все еще подключиться к основной реплике. Тем не менее будет увеличена задержка, так как подключения SQL Server пересекают регионы. В этом случае следует выполнить отработку отказа вручную следующим образом:

   1. Временно переключите базу данных SQL Server в дополнительный регион для *синхронной* фиксации. Это гарантирует, что при отработке отказа не будет потери данных.
   2. Выполните отработку отказа с переходом к этой реплике.
   3. При восстановлении размещения в основном регионе восстановите параметр асинхронной фиксации.

## <a name="manageability-considerations"></a>Вопросы управляемости

При обновлении развертывания обновляйте один регион за раз, чтобы уменьшить вероятность глобального сбоя из-за неправильной конфигурации или ошибки в приложении.

Проверьте устойчивость системы к сбоям. Ниже приведены некоторые распространенные сценарии сбоев для тестирования:

- завершение работы экземпляров виртуальной машины;
- нехватка ресурсов, таких как ЦП и память;
- отключение или задержка сети;
- прекращение работы процессов;
- завершение срока действия сертификатов;
- моделирование сбоев оборудования;
- завершение работы службы DNS на контроллерах домена.

Измерьте время восстановления и убедитесь, что оно соответствует вашим бизнес-требованиям. Следует также протестировать комбинации режимов отказа.

## <a name="related-resources"></a>Связанные ресурсы

Вы можете просмотреть следующий [пример сценария Azure](/azure/architecture/example-scenario), в котором описываются конкретные решения, использующие некоторые из этих технологий:

- [Создание многоуровневых веб-приложений для обеспечения высокой доступности и аварийного восстановления в Azure](/azure/architecture/example-scenario/infrastructure/multi-tier-app-disaster-recovery)
- [Создание безопасных веб-приложений с помощью виртуальных машин Windows в Azure](/azure/architecture/example-scenario/infrastructure/regulated-multitier-app)

<!-- links -->

[hybrid-vpn]: ../hybrid-networking/vpn.md
[azure-dns]: /azure/dns/dns-overview
[azure-sla]: https://azure.microsoft.com/support/legal/sla/
[azure-sql-db]: /azure/sql-database/
[health-endpoint-monitoring-pattern]: https://msdn.microsoft.com/library/dn589789.aspx
[azure-cli]: /cli/azure/
[regional-pairs]: /azure/best-practices-availability-paired-regions
[resource groups]: /azure/azure-resource-manager/resource-group-overview
[resource-group-links]: /azure/resource-group-link-resources
[resource-manager-overview]: /azure/azure-resource-manager/resource-group-overview
[services-by-region]: https://azure.microsoft.com/regions/#services
[sql-always-on]: https://msdn.microsoft.com/library/hh510230.aspx
[tablediff]: https://msdn.microsoft.com/library/ms162843.aspx
[tm-configure-failover]: /azure/traffic-manager/traffic-manager-configure-failover-routing-method
[tm-monitoring]: /azure/traffic-manager/traffic-manager-monitoring
[tm-routing]: /azure/traffic-manager/traffic-manager-routing-methods
[tm-sla]: https://azure.microsoft.com/support/legal/sla/traffic-manager
[traffic-manager]: https://azure.microsoft.com/services/traffic-manager
[visio-download]: https://archcenter.blob.core.windows.net/cdn/vm-reference-architectures.vsdx
[vnet-dns]: /azure/virtual-network/manage-virtual-network#change-dns-servers
[vnet-to-vnet]: /azure/vpn-gateway/vpn-gateway-vnet-vnet-rm-ps
[vpn-gateway]: /azure/vpn-gateway/vpn-gateway-about-vpngateways
[wsfc]: https://msdn.microsoft.com/library/hh270278.aspx
