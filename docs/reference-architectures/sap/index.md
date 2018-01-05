---
title: "Развертывание SAP NetWeaver и SAP HANA в Azure"
description: "Методики по запуску SAP HANA в среде с высоким уровнем доступности в Azure."
author: njray
ms.date: 06/29/2017
ms.openlocfilehash: 27a97103c0c6f305cb8e830d670c8d0ba7e22aa5
ms.sourcegitcommit: b0482d49aab0526be386837702e7724c61232c60
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/14/2017
---
# <a name="deploy-sap-netweaver-and-sap-hana-on-azure"></a>Развертывание SAP NetWeaver и SAP HANA в Azure

В этой эталонной архитектуре показан набор методик по запуску SAP HANA в среде с высоким уровнем доступности в Azure. [**Развернуть это решение**.](#deploy-the-solution)

![0][0]

*Скачайте [файл Visio][visio-download] этой архитектуры.*

> [!NOTE]
> Для развертывания этой эталонной архитектуры требуется соответствующее лицензирование продуктов SAP и другие технологии сторонних производителей. Сведения о партнерстве между корпорацией Майкрософт и SAP см. в статье о [SAP HANA в Azure][sap-hana-on-azure].

## <a name="architecture"></a>Архитектура

Архитектура состоит из следующих компонентов.

- **Виртуальная сеть**. Виртуальная сеть — это представление логически изолированной сети в Azure. Все виртуальные машины в этой эталонной архитектуре развертываются в ту же виртуальную сеть. Затем виртуальная сеть подразделяется на подсети. Создайте отдельную подсеть для каждого уровня, включая приложение (SAP NetWeaver), базу данных (SAP HANA), среду управления (jumpbox) и Active Directory.

- **Виртуальные машины**. Виртуальные машины для этой архитектуры группируются в несколько различных уровней.

    - **SAP NetWeaver**. Включает SAP ASCS, веб-диспетчер SAP и серверы приложений SAP. 
    
    - **SAP HANA**. Эталонная архитектура использует SAP HANA на уровне базы данных, работающем на одном экземпляре [GS5][vm-sizes-mem]. SAP HANA сертифицирована для рабочих нагрузок OLAP на ВМ GS5 или в решении [Крупные экземпляры SAP HANA в Azure][azure-large-instances]. Эта эталонная архитектура предназначена для виртуальных машин Azure серии G и M. Дополнительные сведения о SAP HANA в крупных экземплярах Azure см. в статье [Обзор и описание архитектуры SAP HANA в Azure (крупные экземпляры)][azure-large-instances].
   
    - **Jumpbox**. Он также называется узлом-бастионом. Это безопасная виртуальная машина в сети, которую администраторы используют для подключения к другим виртуальным машинам. 
     
    - **Контроллеры домена Windows Server Active Directory (AD)**. Контроллеры домена используются для настройки отказоустойчивого кластера Windows Server (см. ниже).
 
- **Группы доступности**. Разместите виртуальные машины для ролей веб-диспетчера SAP, сервера приложения SAP и SAP ACSC в отдельные группы доступности, а также подготовьте по крайней мере две виртуальные машины для каждой роли. Таким образом виртуальные машины станут подходящими для соглашения об уровне обслуживания (SLA) более высокого уровня.
    
- **Сетевые адаптеры**. Виртуальным машинам под управлением SAP NetWeaver и SAP HANA нужно два сетевых адаптера (NIC). Каждый сетевой адаптер присваивается другой подсети для разделения различных типов трафика. Дополнительные сведения см. в [разделе рекомендаций](#recommendations).

- **Отказоустойчивый кластер Windows Server**. Виртуальные машины, выполняющие SAP ACSC, настроены в качестве отказоустойчивого кластера для обеспечения высокого уровня доступности. Чтобы поддержать отказоустойчивый кластер, SIOS DataKeeper Cluster Edition выполняет функции общего тома кластера (CSV), реплицируя независимые диски, принадлежащие узлам кластера. Дополнительные сведения см. в записи блога о [запуске приложений SAP на платформе Майкрософт][running-sap].
    
- **Подсистемы балансировки нагрузки.** Используется два экземпляра [Azure Load Balancer][azure-lb]. Первый, который показан слева на схеме, распределяет трафик в виртуальные машины веб-диспетчера SAP. Эта конфигурация реализует вариант параллельных веб-диспетчеров, описанный в статье [High Availability of the SAP Web Dispatcher][sap-dispatcher-ha] (Высокий уровень доступности веб-диспетчера SAP). Вторая подсистема балансировки нагрузки, показанная справа, включает отработку отказа на отказоустойчивом кластере Windows Server путем направления входящих подключений на работоспособный или активный узел.

- **VPN-шлюз.** VPN-шлюз расширяет локальную сеть до виртуальной сети Azure. Можно также использовать канал ExpressRoute, применяющий закрытое выделенное подключение, которое не проходит через общедоступный Интернет. Пример решения не развертывает шлюз. Дополнительные сведения см. в статье [Connect an on-premises network to Azure][hybrid-networking] (Подключение локальной сети к Azure).

## <a name="recommendations"></a>Рекомендации

Требования могут отличаться от архитектуры, описанной здесь. Воспользуйтесь этими рекомендациями в качестве отправной точки.

### <a name="load-balancers"></a>Балансировщики нагрузки

[Веб-диспетчер SAP][sap-dispatcher] обрабатывает балансировку нагрузки трафика HTTP(S) к серверам с двумя стеками (ABAP и Java). SAP много лет поддерживает серверы приложений с одним стеком, поэтому в настоящее время в модели развертывания с двумя стеками выполняется очень мало приложений. Подсистема балансировки нагрузки Azure, показанная на схеме архитектуры, реализует кластер с высоким уровнем доступности для веб-диспетчера SAP.

Балансировка нагрузки трафика на серверы приложений осуществляется в SAP. Для трафика от клиентов SAPGUI, подключающихся к серверу SAP через DIAG и удаленные вызовы функций (RFC), сервер сообщений SCS балансирует нагрузку путем создания [групп входа][logon-groups] сервера приложений SAP. 

SMLG — это транзакция SAP ABAP, используемая для управления возможностями балансировки нагрузки входа в систему центральных служб SAP. Внутренний пул группы входа имеет более одного сервера приложений ABAP. Клиенты, получающие доступ к службам кластера ASCS, подключатся к балансировщику нагрузки Azure через интерфейсный IP-адрес. Имя виртуальной сети кластера ASCS также включает IP-адрес. При необходимости этот адрес можно связать с дополнительным IP-адресом в балансировщике нагрузки Azure, чтобы кластером можно было управлять удаленно.  

### <a name="nics"></a>сетевые карты;

Функции управления ландшафтом SAP требуют разделения трафика сервера на разные сетевые адаптеры. Например, бизнес-данные должны быть отделены от административного трафика и трафика резервного копирования. Назначение нескольких сетевых адаптеров в разные подсети обеспечивает это разделение данных. Дополнительные сведения см. в разделе о сети технического руководства в формате PDF [Building High Availability for SAP NetWeaver and SAP HANA on Linux][sap-ha] (Обеспечение высокого уровня доступности для SAP NetWeaver и SAP HANA в Linux).

Присвойте сетевой адаптер администрирования подсети управления, а сетевой адаптер обмена данными отдельной подсети. Дополнительные сведения о конфигурации см. в статье [Создание виртуальной машины Windows, использующей несколько сетевых адаптеров, и управление ею][multiple-vm-nics].

### <a name="azure-storage"></a>Хранилище Azure

С виртуальными машинами сервера базы данных рекомендуется использовать хранилище Azure класса Premium для согласованных задержек чтения и записи. Для серверов приложений SAP, включая виртуальные машины (A)SCS, можно использовать службу хранилища Azure класса Standard, так как выполнение приложений происходит в памяти и диски используются только для ведения журнала.

Для повышения надежности мы рекомендуем использовать [Управляемые диски Azure][managed-disks]. Использование Управляемых дисков обеспечивает изоляцию дисков для виртуальных машин в группе доступности, что помогает избежать единых точек отказа.

> [!NOTE]
> Сейчас в шаблоне Resource Manager для этой эталонной архитектуры управляемые диски не используются. Мы намерены обновить шаблон для использования управляемых дисков.

Чтобы добиться высокой скорости операции ввода-вывода и лучшей пропускной способности диска, к структуре хранилища Azure применяются распространенные методы оптимизации производительности тома хранилища. Например, чередование нескольких дисков вместе для создания большего тома диска повышает производительность операций ввода-вывода. Включение кэша чтения в содержимом хранилища, которое редко изменяется, повышает скорость извлечения данных. Дополнительные сведения о требованиях к производительности см. в [примечании SAP 1943937 о средстве проверки конфигурации оборудования][sap-1943937].

Для хранения данных резервных копий рекомендуется использовать [холодный уровень хранилища][cool-blob-storage] BLOB-объектов Azure. Холодный уровень хранилища — это эффективный метод хранения данных, которые редко используются и имеют большой срок хранения.

## <a name="scalability-considerations"></a>Вопросы масштабируемости

На прикладном уровне SAP Azure предлагает широкий диапазон размеров виртуальных машин для масштабирования. Полный список см. в [примечании SAP № 1928533 о поддерживаемых продуктах и типах виртуальных машин Azure для приложений SAP в Azure][sap-1928533]. Выполняйте горизонтальное масштабирование путем добавления виртуальных машин в группу доступности.

Для SAP HANA на виртуальных машинах Azure с приложениями SAP OLTP и OLAP размер виртуальной машины, сертифицированной SAP, — GS5 с единым экземпляром виртуальной машины. Для больших рабочих нагрузок корпорация Майкрософт также предлагает [большие экземпляры Azure][azure-large-instances] для SAP HANA на физических серверах, расположенных в центре обработки данных, сертифицированном Microsoft Azure, который на данный момент предоставляет объем памяти до 4 ТБ для одного экземпляра. Также возможны конфигурации с несколькими узлами и общей емкостью памяти до 32 ТБ.

## <a name="availability-considerations"></a>Вопросы доступности

В этой распределенной установке приложения SAP в централизованной базе данных базовая установка реплицируется для обеспечения высокой доступности. Для каждого слоя архитектуры способ обеспечения высокого уровня доступности варьируется:

- **Веб-диспетчер.** Высокий уровень доступности достигается с помощью избыточного количества экземпляров веб-диспетчера SAP с трафиком приложения SAP. Дополнительные сведения см. в документации по [веб-диспетчеру SAP][swd].

- **ASCS.** Для высокого уровня доступности ASCS на виртуальных машинах Windows в Azure используется отказоустойчивая кластеризация Windows Sever с SIOS DataKeeper для реализации общего тома кластера. Дополнительные сведения о реализации см. в [этой статье][clustering].

- **Серверы приложений.** Высокий уровень доступности достигается путем балансировки нагрузки трафика в пуле серверов приложений.

- **Уровень базы данных.** Эта эталонная архитектура развертывает один экземпляр базы данных SAP HANA. Для обеспечения высокой доступности разверните более одного экземпляра и используйте систему репликации HANA для перехода на другой ресурс вручную. Чтобы включить автоматический переход на другой ресурс, требуется расширение HA для определенного дистрибутива Linux.

### <a name="disaster-recovery-considerations"></a>Рекомендации по аварийному восстановлению

Каждый уровень использует разную стратегию обеспечения защиты посредством аварийного восстановления.

- **Серверы приложений.** Серверы приложений SAP не содержат бизнес-данных. В Azure простой стратегией аварийного восстановления является создание серверов приложений SAP в другом регионе. При любых изменениях конфигурации или обновлениях ядра на основном сервере приложений те же изменения должны быть скопированы в виртуальные машины в регионе аварийного восстановления. Например, исполняемые файлы ядра копируются на виртуальные машины аварийного восстановления.

- **Центральные службы SAP.** Этот компонент стека приложений SAP не сохраняет бизнес-данные. Можно создать виртуальную машину в регионе аварийного восстановления для выполнения роли SCS. Единственное содержимое основного узла SCS, которое синхронизируется, — это общее содержимое **/sapmnt**. Кроме того, если конфигурация изменяется или происходит обновление ядра на основных серверах SCS, их нужно повторить в SCS аварийного восстановления. Чтобы синхронизировать два сервера, просто используйте регулярное запланированное задание копирования для копирования **/sapmnt** в экземпляр аварийного восстановления. Сведения о процессе создания, копирования и тестирования отработки отказа можно получить, скачав документ [SAP NetWeaver: Building a Hyper-V & Microsoft Azure–based Disaster Recovery Solution][sap-netweaver-dr] (Создание решения аварийного восстановления на основе Microsoft Azure и Hyper-V в SAP NetWeaver) и просмотрев сведения в разделе 4.3 о слое единой точки отказа SAP (ASCS).

- **Уровень базы данных.** Используйте поддерживаемые HANA решения репликации, такие как HSR или репликация хранилища. 

## <a name="manageability-considerations"></a>Вопросы управляемости

В SAP HANA есть функция резервного копирования, которая использует базовую инфраструктуру Azure. Чтобы создать резервную копию базы данных SAP HANA, выполняющейся на виртуальных машинах Azure, для обеспечения согласованности файлов резервных копий используются и моментальные снимки SAP HANA, и моментальные снимки хранилища Azure. Дополнительные сведения см. в статье [Руководство по резервному копированию SAP HANA на виртуальных машинах Azure][hana-backup] и [Вопросы о службе архивации Azure][backup-faq].

Azure предоставляет несколько функций для [мониторинга и диагностики][monitoring] во всей инфраструктуре. Кроме того, расширенный мониторинг виртуальных машин Azure (Windows или Linux) обрабатывается с помощью Azure Operations Management Suite (OMS).

Чтобы обеспечить мониторинг ресурсов на основе SAP и производительности инфраструктуры SAP, используйте расширение улучшенного мониторинга Azure для SAP. Это расширение передает статистику мониторинга Azure в приложение SAP для мониторинга операционной системы и функций DBACockpit. 

## <a name="security-considerations"></a>Вопросы безопасности

SAP имеет свой собственный механизм управления пользователями для управления доступом на основе ролей и авторизации в приложении SAP. Дополнительные сведения см. в статье [SAP HANA Security - An Overview][sap-security] (Обзор безопасности SAP HANA). (Для доступа необходима учетная запись SAP Service Marketplace.)

Для обеспечения безопасности инфраструктуры данные защищаются путем шифрования при передаче и на месте. С помощью указаний в разделе "Рекомендации по безопасности" в статье [SAP NetWeaver на виртуальных машинах Windows. Руководство по планированию и внедрению][netweaver-on-azure] начните реализовать защиту сети. В руководстве также указаны порты сети, которые необходимо открыть на брандмауэрах, чтобы разрешить взаимодействие с приложением. 

Чтобы выполнить шифрование дисков виртуальной машины Windows и Linux IaaS, можно использовать [шифрование диска Azure][disk-encryption]. Шифрование дисков Azure использует функции (BitLocker в Windows и DM-Crypt в Linux), которые обеспечивают шифрование томов для дисков ОС и дисков данных. Шифрование дисков Azure также работает в Azure Key Vault, что позволяет управлять секретами и ключами шифрования дисков в подписке Key Vault и контролировать их. Данные на дисках виртуальной машины шифруются в хранилище Azure, когда они неактивны.

Для шифрования неактивных данных SAP HANA рекомендуется использовать собственную технологию шифрования SAP HANA.

> [!NOTE]
> Не применяйте шифрование неактивных данных HANA и шифрование диска Azure на том же сервере.

Рассмотрите возможность использования [групп безопасности сети][nsg], чтобы ограничить трафик между различными подсетями в виртуальной сети.

## <a name="deploy-the-solution"></a>Развертывание решения 

Скрипты развертывания для этой эталонной архитектуры можно найти на [GitHub][github].


### <a name="prerequisites"></a>Предварительные требования

- Для завершения установки необходимо иметь доступ к центру загрузки программного обеспечения SAP.
 
- Установите [Azure PowerShell][azure-ps] последней версии. 

- Для этого развертывания требуется 51 ядро. Перед развертыванием убедитесь, что подписка имеет достаточные квоты для ядер виртуальных машин. В противном случае используйте портал Azure, чтобы отправить запрос на увеличение квоты.
 
- Это развертывание использует виртуальную машину серии GS. Проверьте доступность серии GS для каждого региона [здесь][region-availability].

- Для оценки стоимости развертывания перейдите к [калькулятору цен][azure-pricing]. 
 
Эта эталонная архитектура развертывает такие виртуальные машины:

| Имя ресурса | Размер виртуальной машины | Назначение  |
|---------------|---------|----------|
| `ra-sapApps-scs-vm1` ... `ra-sapApps-scs-vmN` | DS11v2 | Центральные службы SAP |
| `ra-sapApps-vm1` ... `ra-sapApps-vmN` | DS11v2 | Приложение SAP NetWeaver |
| `ra-sap-wdp-vm1` ... `ra-sap-wdp-vmN` | DS11v2 | Веб-диспетчер SAP |
| `ra-sap-data-vm1` | GS5 | Экземпляр базы данных SAP HANA |
| `ra-sap-jumpbox-vm1` | DS1V2 | Jumpbox |

Развертывается один экземпляр SAP HANA. Для виртуальных машин приложения количество развертываемых экземпляров указано в параметрах шаблона.

### <a name="deploy-sap-infrastructure"></a>Развертывание инфраструктуры SAP

Эту архитектуру можно развертывать последовательно или всю сразу. Впервые мы рекомендуем выполнять пошаговое развертывание, чтобы вы могли видеть, какие действия выполняются на каждом шаге. Выберите приращение с помощью одного из следующих параметров *режима*.

| Режим           | Действие                                                                                                            |
|----------------|-----------------------------------------------------|
| infrastructure | Развертывание сетевой инфраструктуры в Azure.        |
| workload       | Развертывает серверы SAP в сеть.             |
| все            | Выполняет все предыдущие развертывания.              |

Чтобы развернуть решение, сделайте следующее:

1. Скачайте или клонируйте [репозиторий GitHub][github] на локальный компьютер.

2. Откройте окно PowerShell и перейдите к папке `/sap/sap-hana/`.

3. Выполните приведенный ниже командлет PowerShell. В качестве значения `<subscription id>` введите идентификатор подписки Azure. В качестве значения `<location>` укажите регион Azure (например, `eastus` или `westus`). В качестве значения `<mode>` укажите один из перечисленных выше режимов.

    ```powershell
     .\Deploy-ReferenceArchitecture -SubscriptionId <subscription id> -Location <location> -ResourceGroupName <resource group> <mode>
    ```

4.  При появлении соответствующего запроса войдите в учетную запись Azure. 

Выполнение сценариев развертывания может занять несколько часов в зависимости от выбранного режима.

> [!WARNING]
> Файлы параметров содержат жестко заданный пароль (`AweS0me@PW`) в различных расположениях. Измените эти значения перед развертыванием.
 
### <a name="configure-sap-applications-and-database"></a>Настройка приложений и базы данных SAP

После развертывания инфраструктуры SAP установите и настройте приложения SAP и базу данных HANA на виртуальных машинах следующим образом.

> [!NOTE]
> Вам нужны имя пользователя и пароль портала поддержки SAP для скачивания [руководств по установке SAP][sap-guide].

1. Войдите в jumpbox (`ra-sap-jumpbox-vm1`). jumpbox используется для входа в другие виртуальные машины. 

2.  Для каждой виртуальной машины с именем `ra-sap-wdp-vm1` ... `ra-sap-wdp-vmN` войдите на ВМ и установите и настройте экземпляр веб-диспетчера SAP, выполнив действия, описанные на вики-странице по [установке веб-диспетчера][sap-dispatcher-install].

3.  Войдите на виртуальную машину с именем `ra-sap-data-vm1`. Установите и настройте экземпляр базы данных SAP HANA, используя [руководство по установке и обновлению сервера SAP HANA][hana-guide].

4. Для каждой виртуальной машины с именем `ra-sapApps-scs-vm1` ... `ra-sapApps-scs-vmN` войдите на ВМ, установите и настройте центральные службы SAP (SCS) с помощью [руководств по установке SAP][sap-guide].

5.  Для каждой виртуальной машины с именем `ra-sapApps-vm1` ... `ra-sapApps-vmN` войдите на ВМ, установите и настройте приложение SAP NetWeaver с помощью [руководств по установке SAP][sap-guide].

**_Авторы этой эталонной архитектуры_** &mdash; Рик Рейни (Rick Rainey), Росс Шпонгольц (Ross Sponholtz), Бэн Чинь (Ben Trinh).

[azure-large-instances]: /azure/virtual-machines/workloads/sap/hana-overview-architecture
[azure-lb]: /azure/load-balancer/load-balancer-overview
[azure-pricing]: https://azure.microsoft.com/pricing/calculator/
[azure-ps]: /powershell/azure/overview
[backup-faq]: /azure/backup/backup-azure-backup-faq
[clustering]: https://blogs.msdn.microsoft.com/saponsqlserver/2015/05/20/clustering-sap-ascs-instance-using-windows-server-failover-cluster-on-microsoft-azure-with-sios-datakeeper-and-azure-internal-load-balancer/
[cool-blob-storage]: /azure/storage/storage-blob-storage-tiers
[disk-encryption]: /azure/security/azure-security-disk-encryption
[github]: https://github.com/mspnp/reference-architectures/tree/master/sap/sap-hana
[hana-backup]: /azure/virtual-machines/workloads/sap/sap-hana-backup-guide
[hana-guide]: https://help.sap.com/viewer/2c1988d620e04368aa4103bf26f17727/2.0.01/en-US/7eb0167eb35e4e2885415205b8383584.html
[hybrid-networking]: ../hybrid-networking/index.md
[logon-groups]: https://wiki.scn.sap.com/wiki/display/SI/ABAP+Logon+Group+based+Load+Balancing
[managed-disks]: /azure/storage/storage-managed-disks-overview
[monitoring]: /azure/architecture/best-practices/monitoring
[multiple-vm-nics]: /azure/virtual-machines/windows/multiple-nics
[netweaver-on-azure]: /azure/virtual-machines/workloads/sap/planning-guide
[nsg]: /azure/virtual-network/virtual-networks-nsg
[region-availability]: https://azure.microsoft.com/regions/services/
[running-SAP]: https://blogs.msdn.microsoft.com/saponsqlserver/2016/06/07/sap-on-sql-general-update-for-customers-partners-june-2016/
[sap-1943937]: https://launchpad.support.sap.com/#/notes/1943937
[sap-1928533]: https://launchpad.support.sap.com/#/notes/1928533
[sap-dispatcher]: https://help.sap.com/doc/saphelp_nw73ehp1/7.31.19/en-US/48/8fe37933114e6fe10000000a421937/frameset.htm
[sap-dispatcher-ha]: https://help.sap.com/doc/saphelp_nw73ehp1/7.31.19/en-US/48/9a9a6b48c673e8e10000000a42189b/frameset.htm
[sap-dispatcher-install]: https://wiki.scn.sap.com/wiki/display/SI/Web+Dispatcher+Installation
[sap-guide]: https://service.sap.com/instguides
[sap-ha]: https://support.sap.com/content/dam/SAAP/SAP_Activate/AGS_70.pdf
[sap-hana-on-azure]: https://azure.microsoft.com/services/virtual-machines/sap-hana/
[sap-netweaver-dr]: http://download.microsoft.com/download/9/5/6/956FEDC3-702D-4EFB-A7D3-2DB7505566B6/SAP%20NetWeaver%20-%20Building%20an%20Azure%20based%20Disaster%20Recovery%20Solution%20V1_5%20.docx
[sap-security]: https://archive.sap.com/documents/docs/DOC-62943
[visio-download]: https://archcenter.azureedge.net/cdn/SAP-HANA-architecture.vsdx
[vm-sizes-mem]: /azure/virtual-machines/windows/sizes-memory
[swd]: https://help.sap.com/doc/saphelp_nw70ehp2/7.02.16/en-us/48/8fe37933114e6fe10000000a421937/frameset.htm
[0]: ./images/sap-hana.png "Архитектура SAP HANA с использованием Microsoft Azure".