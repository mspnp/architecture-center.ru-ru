---
title: Запуск виртуальной машины Linux в Azure
description: Сведения о выполнении одной виртуальной машины Linux в Azure с учетом требований к масштабируемости, устойчивости, управляемости и безопасности.
author: telmosampaio
ms.date: 04/03/2018
ms.openlocfilehash: f29b7225c2e0edbb1569c9e3a55d112d12041af8
ms.sourcegitcommit: a5e549c15a948f6fb5cec786dbddc8578af3be66
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/06/2018
---
# <a name="run-a-linux-vm-on-azure"></a>Запуск виртуальной машины Linux в Azure

В этой статье описаны проверенные способы запуска виртуальных машин Linux в Azure. Она содержит рекомендации для подготовки виртуальной машины, а также компоненты хранилища и сетевые компоненты. [**Разверните это решение**.](#deploy-the-solution)

![[0]][0]

## <a name="components"></a>Компоненты

Для подготовки виртуальной машины Azure требуются некоторые дополнительные компоненты, помимо самой виртуальной машины, такие как сеть и хранилище.

* **Группа ресурсов.** [Группа ресурсов][resource-manager-overview] представляет собой логический контейнер, содержащий связанные ресурсы Azure. Как правило, группы ресурсов создаются в зависимости от времени существования ресурсов и пользователя, который ими управляет. 

* **Виртуальная машина**. Виртуальную машину можно подготовить на основе списка опубликованных образов, пользовательского управляемого образа или файла виртуального жесткого диска (VHD), передав его в хранилище BLOB-объектов Azure. Azure поддерживает ряд популярных дистрибутивов Linux, включая CentOS, Debian, Red Hat Enterprise, Ubuntu и FreeBSD. См. дополнительные сведения о [взаимодействии Azure и Linux][azure-linux].

* **Управляемые диски.** Служба [Управляемые диски Azure][managed-disks] упрощает управление дисками благодаря автоматической обработке хранилища. Диск операционной системы — это диск VHD, расположенный в [службе хранилища Azure][azure-storage], поэтому он работает, даже если хост-компьютер отключен. Для виртуальных машин Linux диск ОС — это `/dev/sda1`. Кроме того, рекомендуем создать один или несколько [дисков данных][data-disk] на постоянных виртуальных жестких дисках, которые используются для данных приложений. 

* **Временный диск.** Виртуальная машина создается с временным диском. Временный диск хранится на физическом диске хост-компьютера. Он *не* хранится в службе хранилища Azure и может быть удален во время перезагрузки и других событий жизненного цикла виртуальной машины. Используйте этот диск только для временных данных, таких как данные страниц или файлы подкачки. Для виртуальных машин Linux временный диск — это `/dev/sdb1`. Он подключен как `/mnt/resource` или `/mnt`.

* **Виртуальная сеть.** Каждая виртуальная машина Azure развертывается в виртуальной сети, которую можно разделить на несколько подсетей.

* **Сетевой интерфейс (сетевой адаптер)**. Сетевой адаптер обеспечивает взаимодействие виртуальной машины и виртуальной сети.

* **Общедоступный IP-адрес.** Общедоступный IP-адрес используется для обмена данных с виртуальной машиной &mdash;, например, по протоколу SSH.

* **Azure DNS**. [Azure DNS][azure-dns] — это служба размещения для доменов DNS, которая предоставляет разрешение имен с помощью инфраструктуры Microsoft Azure. Размещая домены в Azure, вы можете управлять своими записями DNS с помощью тех же учетных данных, API и инструментов и оплачивать использование, как и другие службы Azure.

* **Группа безопасности сети**. [Группы безопасности сети][nsg] позволяют разрешать или запрещать сетевой трафик к виртуальным машинам. Группы безопасности сети могут быть связаны с подсетями или отдельными экземплярами виртуальных машин.

* **Диагностика.** Ведение журналов диагностики очень важно для устранения неполадок виртуальной машины и управления ею.

## <a name="vm-recommendations"></a>Рекомендации по виртуальным машинам

В Azure доступны виртуальные машины разных размеров. Дополнительные сведения см. в статье [Размеры виртуальных машин Windows в Azure][virtual-machine-sizes]. При перемещении имеющейся рабочей нагрузки в Azure выберите начальный размер виртуальной машины, который точнее всего соответствует характеристикам локальных серверов. Затем измерьте производительность фактической рабочей нагрузки по таким показателям, как потребление ЦП, памяти и дисковых операций ввода-вывода в секунду, и при необходимости измените размер виртуальной машины. Если для виртуальной машины требуется несколько сетевых адаптеров, помните, что их максимально возможное число зависит от [размера каждой виртуальной машины][vm-size-tables].

Обычно следует выбирать регион Azure, расположенный как можно ближе к внутренним пользователям или клиентам. Но виртуальные машины некоторых размеров доступны не во всех регионах. См. дополнительные сведения о [доступности служб в разных регионах][services-by-region]. Чтобы получить список размеров виртуальных машин, доступных в определенном регионе, выполните следующую команду в интерфейсе командной строки Azure:

```
az vm list-sizes --location <location>
```

Дополнительные сведения о том, как выбрать опубликованный образ виртуальной машины, см. в руководстве по [поиску образов виртуальных машин Linux][select-vm-image].

Включите мониторинг и диагностику, в том числе базовые метрики работоспособности, а также ведение журналов инфраструктуры диагностики и [диагностику загрузки][boot-diagnostics]. Если виртуальную машину невозможно загрузить, для обнаружения неисправностей можно использовать диагностику загрузки. Дополнительные сведения см. в статье [Включение мониторинга и диагностики][enable-monitoring].  

## <a name="disk-and-storage-recommendations"></a>Рекомендации по дискам и хранилищам

Чтобы обеспечить оптимальную производительность дисковых операций ввода-вывода, рекомендуем использовать [хранилище класса Premium][premium-storage], в котором данные хранятся на твердотельных накопителях (SSD). Цена зависит от производительности подготовленного диска. Скорость выполнения операций ввода-вывода и пропускная способность (т. е. скорость передачи данных) также зависят от размера диска. Поэтому во время подготовки диска следует учитывать все эти факторы. 

Мы также рекомендуем использовать [Управляемые диски][managed-disks]. Управляемым дискам не требуется учетная запись хранения. Просто укажите размер и тип диска, и он будет развернут как высокодоступный ресурс.

Добавьте один или несколько дисков данных. Создаваемый диск VHD не форматируется. Чтобы отформатировать диск, войдите в систему виртуальной машины. В оболочке Linux диски данных отображаются как `/dev/sdc`, `/dev/sdd` и т. д. Можно выполнить `lsblk` , чтобы вывести список блочных устройств, включая диски. Чтобы использовать диск данных, создайте раздел и файловую систему, а затем подключите этот диск. Например: 

```bat
# Create a partition.
sudo fdisk /dev/sdc     # Enter 'n' to partition, 'w' to write the change.

# Create a file system.
sudo mkfs -t ext3 /dev/sdc1

# Mount the drive.
sudo mkdir /data1
sudo mount /dev/sdc1 /data1
```

При добавлении диска данных ему назначается логический номер устройства (LUN). При необходимости можно указать идентификатор LUN &mdash; например, если при замене диска нужно сохранить тот же идентификатор LUN или если у вас есть приложение, которое ищет определенный идентификатор LUN. Однако следует помнить, что идентификаторы LUN для каждого диска должны быть уникальными.

Возможно, вам нужно изменить планировщик операций ввода-вывода, чтобы оптимизировать производительность твердотельных накопителей (используемых с учетными записями хранения класса Premium для виртуальных машин). Обычно для SSD рекомендуется использовать планировщик NOOP, но для мониторинга производительности дискового ввода-вывода рабочей нагрузки следует использовать такой инструмент, как [iostat].

Создайте учетную запись хранения для хранения журналов диагностики. Учетной записи локально избыточного хранилища достаточно для хранения журналов диагностики.

> [!NOTE]
> Если вы не используете Управляемые диски, создайте отдельные учетные записи хранения Azure для виртуальных жестких дисков (VHD) каждой виртуальной машины, чтобы не превышать [ограничения по операциям ввода-вывода][vm-disk-limits] для учетных записей хранения. Учтите общие ограничения на количество операций ввода-вывода для учетной записи хранения. Дополнительные сведения см. в разделе [Ограничения для дисков виртуальной машины][vm-disk-limits].

## <a name="network-recommendations"></a>Рекомендации по сети

Общедоступный IP-адрес может быть динамическим или статическим. По умолчанию используется динамический IP-адрес.

* Зарезервируйте [статический IP-адрес][static-ip], если вам нужен постоянный IP-адрес &mdash; например, для создания записи А в DNS или добавления IP-адреса в список надежных отправителей.
* Можно также создать полное доменное имя для IP-адреса. Затем вы сможете зарегистрировать в DNS [запись CNAME][cname-record], которая указывает на полное доменное имя. Дополнительные сведения см. в руководстве по [созданию полного доменного имени на портале Azure][fqdn]. Можно использовать [Azure DNS][azure-dns] или другую службу DNS.

Все группы безопасности сети содержат набор [правил по умолчанию][nsg-default-rules], включая правило, которое блокирует весь входящий интернет-трафик. Правила по умолчанию нельзя удалить, но их можно переопределить другими правилами. Чтобы разрешить интернет-трафик, создайте правила, разрешающие входящий трафик для определенных портов &mdash; например, это может быть порт 80 для протокола HTTP.

Чтобы включить доступ по протоколу SSH, добавьте правило группы безопасности сети, которое разрешает входящий трафик через TCP-порт 22.

## <a name="scalability-considerations"></a>Вопросы масштабируемости

Размер виртуальной машины можно [увеличивать и уменьшать][vm-resize]. Для горизонтального развертывания разместите две или больше виртуальных машин за подсистемой балансировки нагрузки. Дополнительные сведения см. в руководстве по [эталонной архитектуре n-уровневых приложений](./n-tier-cassandra.md).

## <a name="availability-considerations"></a>Вопросы доступности

Для повышения уровня доступности разверните несколько виртуальных машин в группе доступности. Это также позволит использовать дополнительные услуги в рамках [соглашения об уровне обслуживания][vm-sla].

На виртуальную машину могут влиять процедуры [планового][planned-maintenance] и [внепланового технического обслуживания][manage-vm-availability]. Чтобы определить, вызвана ли перезагрузка плановым техническим обслуживанием, изучите [журналы перезагрузки виртуальной машины][reboot-logs].

Чтобы избежать случайной потери данных во время обычной работы (например, из-за ошибки пользователя), следует реализовать создание резервных копий на определенный момент времени с помощью [моментальных снимков больших двоичных объектов][blob-snapshot] или других инструментов.

## <a name="manageability-considerations"></a>Вопросы управляемости

**Группы ресурсов.** Поместите тесно связанные ресурсы с одинаковым жизненным циклом в одну [группу ресурсов][resource-manager-overview]. Группы ресурсов позволяют развертывать и отслеживать несколько ресурсов как одну группу, для которой, помимо прочего, можно отслеживать выставляемые счета. Можно также удалить ресурсы в виде набора, что очень удобно для тестирования развернутых служб. Присвойте понятные имена ресурсам, чтобы упростить поиск определенного ресурса и получить сведения о его роли. Дополнительные сведения см. в руководстве по [рекомендуемым соглашениям об именовании ресурсов Azure][naming-conventions].

**SSH.** Перед созданием виртуальной машины Linux создайте парные 2048-разрядные ключи RSA (открытый и закрытый). При создании виртуальной машины используйте файл открытого ключа. Дополнительные сведения см. в статье [Создание пары из открытого и закрытого ключей SSH для виртуальных машин Linux][ssh-linux].

**Остановка виртуальной машины.** Azure различает состояния "Остановлена" и "Освобождена". Вы оплачиваете использование остановленных виртуальных машин, но не оплачиваете освобожденные виртуальные машины. Также это можно сделать с помощью кнопки **Прервать** на портале Azure. Если вы войдете в виртуальную машину и завершите работу операционной системы, виртуальная машина будет остановлена, а **не** освобождена, поэтому с вас по-прежнему будет взиматься плата.

**Удаление виртуальной машины.** Если вы удалите виртуальную машину, виртуальные жесткие диски останутся. Это означает, что вы можете удалить виртуальную машину без потери данных. Тем не менее плата за хранение по-прежнему будет взиматься. Чтобы удалить виртуальный жесткий диск, удалите соответствующий файл из [хранилища BLOB-объектов][blob-storage]. Чтобы предотвратить случайное удаление, используйте [блокировку ресурсов][resource-lock]. Так вы сможете заблокировать всю группу или отдельные ресурсы (например, виртуальную машину).

## <a name="security-considerations"></a>Вопросы безопасности

Благодаря [центру безопасности Azure][security-center] можно получить полное представление о состоянии безопасности ваших ресурсов Azure. Центр безопасности отслеживает потенциальные проблемы безопасности, а также обеспечивает полное представление о состоянии системы безопасности развертывания. Центр безопасности настраивается на уровне подписки Azure. Включите сбор данных о безопасности, как описано в [кратком руководстве по использованию центра безопасности Azure][security-center-get-started]. Когда сбор данных включен, центр безопасности автоматически проверяет все виртуальные машины, созданные для этой подписки.

**Управление исправлениями.** Если эта функция включена, центр безопасности проверяет наличие необходимых обновлений для системы безопасности и критических обновлений. 

**Защита от вредоносных программ.** Если эта функция включена, то Центр безопасности проверяет, установлена ли антивредоносное ПО. Центр безопасности позволяет также установить антивредоносное ПО с помощью портала Azure.

**Операции.** Используйте [управление доступом на основе ролей (RBAC)][rbac] для управления доступом к развертываемым ресурсам Azure. RBAC позволяет назначить роли авторизации участникам команды DevOps. Например, роль "Читатель" позволяет просматривать ресурсы Azure, но не позволяет создавать и удалять их или управлять ими. Некоторые роли относятся к определенным типам ресурсов Azure. Например, роль "Участник виртуальных машин" позволяет перезапустить виртуальную машину или отменить ее выделение, сбросить пароль администратора, создать новую виртуальную машину и т. д. Для этой архитектуры могут оказаться полезными и другие [встроенные роли RBAC][rbac-roles], например [Пользователь DevTest Labs][rbac-devtest] и [Участник сетей][rbac-network]. Пользователю можно назначить несколько ролей. Можно также создать пользовательские роли, чтобы более детально настроить разрешения.

> [!NOTE]
> RBAC не ограничивает действия, которые может выполнять пользователь, вошедший в виртуальную машину. Эти разрешения определяются типом учетной записи в гостевой ОС.   

Просматривать действия по подготовке и другие события для виртуальной машины можно с помощью [журналов аудита][audit-logs].

**Шифрование данных.** Если нужно шифровать диски ОС и диски данных, рекомендуем применить [шифрование дисков Azure][disk-encryption]. 

## <a name="deploy-the-solution"></a>Развертывание решения

Развертывание доступно в [репозитории GitHub][github-folder]. Он позволяет развернуть следующее:

  * Виртуальную сеть с одной подсетью, называющейся **web**, которая используется для размещения виртуальных машин.
  * Группу безопасности сети с двумя правилами для входящего трафика, позволяющими передавать HTTP- и SSH-трафик в виртуальную машину.
  * Виртуальную машину, на которой выполняется последняя версия Ubuntu 16.04.3 LTS.
  * Пример расширения пользовательских скриптов, которые форматируют два диска данных и развертывают HTTP-сервер Apache в виртуальной машине Ubuntu.

### <a name="prerequisites"></a>предварительным требованиям

1. Клонируйте или скачайте ZIP-файл [с эталонными архитектурами][ref-arch-repo] в репозитории GitHub либо создайте для него вилку.

2. Убедитесь, что Azure CLI 2.0 установлен на компьютере. См. инструкции по [установке Azure CLI 2.0][azure-cli-2].

3. Установите пакет npm [стандартных блоков Azure][azbb].

4. В командной строке операционной системы, Bash или PowerShell введите следующую команду, чтобы войти в учетную запись Azure.

  ```bash
  az login
  ```

5. Создайте пару ключей SSH. Дополнительные сведения см. в статье [Как создать и использовать пару из открытого и закрытого ключей SSH для виртуальных машин Linux в Azure](/azure/virtual-machines/linux/mac-create-ssh-keys).

### <a name="deploy-the-solution-using-azbb"></a>Развертывание решения с помощью azbb

1. Перейдите в папку `virtual-machines/single-vm/parameters/linux` репозитория, скачанного на предыдущем шаге.

2. Откройте файл `single-vm-v2.json` и введите имя пользователя и открытый ключ SSH в кавычках, а затем сохраните файл.

  ```bash
  "adminUsername": "<your username>",
  "sshPublicKey": "ssh-rsa AAAAB3NzaC1...",
  ```

3. Запустите `azbb`, чтобы развернуть пример виртуальной машины, как показано ниже.

  ```bash
  azbb -s <subscription_id> -g <resource_group_name> -l <location> -p single-vm-v2.json --deploy
  ```

Для проверки развертывания выполните следующую команду Azure CLI, чтобы найти общедоступный IP-адрес виртуальной машины:

```bash
az vm show -n ra-single-linux-vm1 -g <resource-group-name> -d -o table
```

По этому адресу в веб-браузере должна открыться домашняя страница Apache2 по умолчанию.

<!-- links -->
[audit-logs]: https://azure.microsoft.com/blog/analyze-azure-audit-logs-in-powerbi-more/
[availability-set]: /azure/virtual-machines/virtual-machines-linux-manage-availability
[azbb]: https://github.com/mspnp/template-building-blocks/wiki/Install-Azure-Building-Blocks
[azbbv2]: https://github.com/mspnp/template-building-blocks
[azure-cli-2]: /cli/azure/install-azure-cli?view=azure-cli-latest
[azure-linux]: /azure/virtual-machines/virtual-machines-linux-azure-overview
[azure-storage]: /azure/storage/storage-introduction
[blob-snapshot]: /azure/storage/storage-blob-snapshots
[blob-storage]: /azure/storage/storage-introduction
[boot-diagnostics]: https://azure.microsoft.com/blog/boot-diagnostics-for-virtual-machines-v2/
[cname-record]: https://en.wikipedia.org/wiki/CNAME_record
[data-disk]: /azure/virtual-machines/virtual-machines-linux-about-disks-vhds
[disk-encryption]: /azure/security/azure-security-disk-encryption
[enable-monitoring]: /azure/monitoring-and-diagnostics/insights-how-to-use-diagnostics
[azure-dns]: /azure/dns/dns-overview
[fqdn]: /azure/virtual-machines/virtual-machines-linux-portal-create-fqdn
[git]: https://github.com/mspnp/reference-architectures/tree/master/virtual-machines/single-vm
[github-folder]: https://github.com/mspnp/reference-architectures/tree/master/virtual-machines/single-vm
[iostat]: https://en.wikipedia.org/wiki/Iostat
[manage-vm-availability]: /azure/virtual-machines/virtual-machines-linux-manage-availability
[managed-disks]: /azure/storage/storage-managed-disks-overview
[naming-conventions]: /azure/architecture/best-practices/naming-conventions.md
[nsg]: /azure/virtual-network/virtual-networks-nsg
[nsg-default-rules]: /azure/virtual-network/virtual-networks-nsg#default-rules
[planned-maintenance]: /azure/virtual-machines/virtual-machines-linux-planned-maintenance
[premium-storage]: /azure/virtual-machines/linux/premium-storage
[premium-storage-supported]: /azure/virtual-machines/linux/premium-storage#supported-vms
[rbac]: /azure/active-directory/role-based-access-control-what-is
[rbac-roles]: /azure/active-directory/role-based-access-built-in-roles
[rbac-devtest]: /azure/active-directory/role-based-access-built-in-roles#devtest-labs-user
[rbac-network]: /azure/active-directory/role-based-access-built-in-roles#network-contributor
[reboot-logs]: https://azure.microsoft.com/blog/viewing-vm-reboot-logs/
[ref-arch-repo]: https://github.com/mspnp/reference-architectures
[resource-lock]: /azure/resource-group-lock-resources
[resource-manager-overview]: /azure/azure-resource-manager/resource-group-overview
[security-center]: /azure/security-center/security-center-intro
[security-center-get-started]: /azure/security-center/security-center-get-started
[select-vm-image]: /azure/virtual-machines/virtual-machines-linux-cli-ps-findimage
[services-by-region]: https://azure.microsoft.com/regions/#services
[ssh-linux]: /azure/virtual-machines/virtual-machines-linux-mac-create-ssh-keys
[static-ip]: /azure/virtual-network/virtual-networks-reserved-public-ip
[virtual-machine-sizes]: /azure/virtual-machines/virtual-machines-linux-sizes
[visio-download]: https://archcenter.blob.core.windows.net/cdn/vm-reference-architectures.vsdx
[vm-disk-limits]: /azure/azure-subscription-service-limits#virtual-machine-disk-limits
[vm-resize]: /azure/virtual-machines/virtual-machines-linux-change-vm-size
[vm-size-tables]: /azure/virtual-machines/virtual-machines-linux-sizes
[vm-sla]: https://azure.microsoft.com/support/legal/sla/virtual-machines
[0]: ./images/single-vm-diagram.png "Отдельная виртуальная машина Linux в Azure"