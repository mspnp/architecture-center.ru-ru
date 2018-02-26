---
title: "Выбор хранилища данных OLTP"
description: 
author: zoinerTejada
ms:date: 02/12/2018
ms.openlocfilehash: 1c27d7d5f3b78f40822de6b77664dbf49b1367f6
ms.sourcegitcommit: 90cf2de795e50571d597cfcb9b302e48933e7f18
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/14/2018
---
# <a name="choosing-an-oltp-data-store-in-azure"></a><span data-ttu-id="82419-102">Выбор хранилища данных OLTP в Azure</span><span class="sxs-lookup"><span data-stu-id="82419-102">Choosing an OLTP data store in Azure</span></span>

<span data-ttu-id="82419-103">Оперативная обработка транзакций (OLTP) — это управление данными о транзакциях и обработкой транзакций.</span><span class="sxs-lookup"><span data-stu-id="82419-103">Online transaction processing (OLTP) is the management of transactional data and transaction processing.</span></span> <span data-ttu-id="82419-104">В этом разделе сравниваются варианты решений OLTP в Azure.</span><span class="sxs-lookup"><span data-stu-id="82419-104">This topic compares options for OLTP solutions in Azure.</span></span>

> [!NOTE]
> <span data-ttu-id="82419-105">Дополнительные сведения об использовании хранилища данных OLTP см. в статье [об оперативной обработке транзакций](../scenarios/online-analytical-processing.md).</span><span class="sxs-lookup"><span data-stu-id="82419-105">For more information about when to use an OLTP data store, see [Online transaction processing](../scenarios/online-analytical-processing.md).</span></span>

## <a name="what-are-your-options-when-choosing-an-oltp-data-store"></a><span data-ttu-id="82419-106">Варианты при выборе хранилища данных OLTP</span><span class="sxs-lookup"><span data-stu-id="82419-106">What are your options when choosing an OLTP data store?</span></span>

<span data-ttu-id="82419-107">Все следующие хранилища данных в Azure соответствуют основным требованиям к OLTP и управлению данными о транзакциях:</span><span class="sxs-lookup"><span data-stu-id="82419-107">In Azure, all of the following data stores will meet the core requirements for OLTP and the management of transaction data:</span></span>

- [<span data-ttu-id="82419-108">база данных SQL Azure;</span><span class="sxs-lookup"><span data-stu-id="82419-108">Azure SQL Database</span></span>](/azure/sql-database/)
- <span data-ttu-id="82419-109">[SQL Server на виртуальной машине Azure](/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-iaas-overview?toc=%2Fazure%2Fvirtual-machines%2Fwindows%2Ftoc.json);</span><span class="sxs-lookup"><span data-stu-id="82419-109">[SQL Server in an Azure virtual machine](/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-iaas-overview?toc=%2Fazure%2Fvirtual-machines%2Fwindows%2Ftoc.json)</span></span>
- [<span data-ttu-id="82419-110">База данных Azure для MySQL</span><span class="sxs-lookup"><span data-stu-id="82419-110">Azure Database for MySQL</span></span>](/azure/mysql/)
- [<span data-ttu-id="82419-111">База данных Azure для PostgreSQL</span><span class="sxs-lookup"><span data-stu-id="82419-111">Azure Database for PostgreSQL</span></span>](/azure/postgresql/)

## <a name="key-selection-criteria"></a><span data-ttu-id="82419-112">Основные критерии выбора</span><span class="sxs-lookup"><span data-stu-id="82419-112">Key selection criteria</span></span>

<span data-ttu-id="82419-113">Чтобы ограничить количество вариантов, сначала ответьте на следующие вопросы:</span><span class="sxs-lookup"><span data-stu-id="82419-113">To narrow the choices, start by answering these questions:</span></span>

- <span data-ttu-id="82419-114">Вы хотите использовать управляемую службу, а не управлять собственными серверами?</span><span class="sxs-lookup"><span data-stu-id="82419-114">Do you want a managed service rather than managing your own servers?</span></span>

- <span data-ttu-id="82419-115">Зависит ли ваше решение от совместимости с Microsoft SQL Server, MySQL или PostgreSQL?</span><span class="sxs-lookup"><span data-stu-id="82419-115">Does your solution have specific dependencies for Microsoft SQL Server, MySQL or PostgreSQL compatibility?</span></span> <span data-ttu-id="82419-116">Количество вариантов для хранилищ данных может быть ограничено приложением из-за драйверов, поддерживаемых для взаимодействия с хранилищем данных, или на основе предположений касательно используемой базы данных.</span><span class="sxs-lookup"><span data-stu-id="82419-116">Your application may limit the data stores you can choose based on the drivers it supports for communicating with the data store, or the assumptions it makes about which database is used.</span></span>

- <span data-ttu-id="82419-117">Выдвигаются ли особенно высокие требования к пропускной способности операций записи?</span><span class="sxs-lookup"><span data-stu-id="82419-117">Are your write throughput requirements particularly high?</span></span> <span data-ttu-id="82419-118">Если да, выберите вариант с поддержкой таблиц в памяти.</span><span class="sxs-lookup"><span data-stu-id="82419-118">If yes, choose an option that provides in-memory tables.</span></span> 

- <span data-ttu-id="82419-119">Является ли ваше решение мультитенантным?</span><span class="sxs-lookup"><span data-stu-id="82419-119">Is your solution multi-tenant?</span></span> <span data-ttu-id="82419-120">Если да, выберите варианты с поддержкой пулов емкости, где большое количество экземпляров баз данных подготавливаются на основе эластичного пула ресурсов, вместо фиксированных ресурсов на одну базу данных.</span><span class="sxs-lookup"><span data-stu-id="82419-120">If so, consider options that support capacity pools, where multiple database instances draw from an elastic pool of resources, instead of fixed resources per database.</span></span> <span data-ttu-id="82419-121">Это позволит лучше распределить емкость для всех экземпляров базы данных и сделать ваше решение более экономичным.</span><span class="sxs-lookup"><span data-stu-id="82419-121">This can help you better distribute capacity across all database instances, and can make your solution more cost effective.</span></span>

- <span data-ttu-id="82419-122">Нужно ли обеспечить чтение данных с низкой задержкой в нескольких регионах?</span><span class="sxs-lookup"><span data-stu-id="82419-122">Does your data need to be readable with low latency in multiple regions?</span></span> <span data-ttu-id="82419-123">Если да, выберите вариант с поддержкой вторичных реплик, доступных для чтения.</span><span class="sxs-lookup"><span data-stu-id="82419-123">If yes, choose an option that supports readable secondary replicas.</span></span>

- <span data-ttu-id="82419-124">Нужно ли обеспечить высокий уровень доступности баз данных в нескольких географических регионах?</span><span class="sxs-lookup"><span data-stu-id="82419-124">Does your database need to be highly available across geo-graphic regions?</span></span> <span data-ttu-id="82419-125">Если да, выберите вариант с поддержкой георепликации.</span><span class="sxs-lookup"><span data-stu-id="82419-125">If yes, choose an option that supports geographic replication.</span></span> <span data-ttu-id="82419-126">Также рассмотрите варианты, которые поддерживают автоматический переход с первичной реплики на вторичную реплику.</span><span class="sxs-lookup"><span data-stu-id="82419-126">Also consider the options that support automatic failover from the primary replica to a secondary replica.</span></span>

- <span data-ttu-id="82419-127">Есть ли у вашей базы данных особые требования к безопасности?</span><span class="sxs-lookup"><span data-stu-id="82419-127">Does your database have specific security needs?</span></span> <span data-ttu-id="82419-128">Если да, выберите варианты, которые предоставляют такие возможности, как безопасность на уровне строк, маскирование данных и прозрачное шифрование данных.</span><span class="sxs-lookup"><span data-stu-id="82419-128">If yes, examine the options that provide capabilities like row level security, data masking, and transparent data encryption.</span></span>

## <a name="capability-matrix"></a><span data-ttu-id="82419-129">Матрица возможностей</span><span class="sxs-lookup"><span data-stu-id="82419-129">Capability matrix</span></span>

<span data-ttu-id="82419-130">В следующих таблицах перечислены основные различия в возможностях.</span><span class="sxs-lookup"><span data-stu-id="82419-130">The following tables summarize the key differences in capabilities.</span></span>

### <a name="general-capabilities"></a><span data-ttu-id="82419-131">Общие возможности</span><span class="sxs-lookup"><span data-stu-id="82419-131">General capabilities</span></span> 
| | <span data-ttu-id="82419-132">Базы данных SQL Azure</span><span class="sxs-lookup"><span data-stu-id="82419-132">Azure SQL Database</span></span> | <span data-ttu-id="82419-133">SQL Server на виртуальной машине Azure</span><span class="sxs-lookup"><span data-stu-id="82419-133">SQL Server in an Azure virtual machine</span></span> | <span data-ttu-id="82419-134">База данных Azure для MySQL</span><span class="sxs-lookup"><span data-stu-id="82419-134">Azure Database for MySQL</span></span> | <span data-ttu-id="82419-135">База данных Azure для PostgreSQL</span><span class="sxs-lookup"><span data-stu-id="82419-135">Azure Database for PostgreSQL</span></span> |
| --- | --- | --- | --- | --- | --- |
| <span data-ttu-id="82419-136">Управляемая служба</span><span class="sxs-lookup"><span data-stu-id="82419-136">Is Managed Service</span></span> | <span data-ttu-id="82419-137">Yes</span><span class="sxs-lookup"><span data-stu-id="82419-137">Yes</span></span> | <span data-ttu-id="82419-138">Нет </span><span class="sxs-lookup"><span data-stu-id="82419-138">No</span></span> | <span data-ttu-id="82419-139">Yes</span><span class="sxs-lookup"><span data-stu-id="82419-139">Yes</span></span> | <span data-ttu-id="82419-140">Yes</span><span class="sxs-lookup"><span data-stu-id="82419-140">Yes</span></span> |
| <span data-ttu-id="82419-141">Запуск на платформе</span><span class="sxs-lookup"><span data-stu-id="82419-141">Runs on Platform</span></span> | <span data-ttu-id="82419-142">Недоступно</span><span class="sxs-lookup"><span data-stu-id="82419-142">N/A</span></span> | <span data-ttu-id="82419-143">Windows, Linux, Docker</span><span class="sxs-lookup"><span data-stu-id="82419-143">Windows, Linux, Docker</span></span> | <span data-ttu-id="82419-144">Недоступно</span><span class="sxs-lookup"><span data-stu-id="82419-144">N/A</span></span> | <span data-ttu-id="82419-145">Недоступно</span><span class="sxs-lookup"><span data-stu-id="82419-145">N/A</span></span> |
| <span data-ttu-id="82419-146">Программируемость <sup>1</sup></span><span class="sxs-lookup"><span data-stu-id="82419-146">Programmability <sup>1</sup></span></span> | <span data-ttu-id="82419-147">T-SQL, .NET, R</span><span class="sxs-lookup"><span data-stu-id="82419-147">T-SQL, .NET, R</span></span> | <span data-ttu-id="82419-148">T-SQL, .NET, R, Python</span><span class="sxs-lookup"><span data-stu-id="82419-148">T-SQL, .NET, R, Python</span></span> | <span data-ttu-id="82419-149">T-SQL, .NET, R, Python</span><span class="sxs-lookup"><span data-stu-id="82419-149">T-SQL, .NET, R, Python</span></span> | <span data-ttu-id="82419-150">SQL</span><span class="sxs-lookup"><span data-stu-id="82419-150">SQL</span></span> | <span data-ttu-id="82419-151">SQL</span><span class="sxs-lookup"><span data-stu-id="82419-151">SQL</span></span> |

<span data-ttu-id="82419-152">[1] Без поддержки клиентского драйвера, что позволяет подключать различные языки программирования и использовать хранилище данных OLTP.</span><span class="sxs-lookup"><span data-stu-id="82419-152">[1] Not including client driver support, which allows many programming languages to connect to and use the OLTP data store.</span></span>

### <a name="scalability-capabilities"></a><span data-ttu-id="82419-153">Масштабируемость</span><span class="sxs-lookup"><span data-stu-id="82419-153">Scalability capabilities</span></span>
| | <span data-ttu-id="82419-154">Базы данных SQL Azure</span><span class="sxs-lookup"><span data-stu-id="82419-154">Azure SQL Database</span></span> | <span data-ttu-id="82419-155">SQL Server на виртуальной машине Azure</span><span class="sxs-lookup"><span data-stu-id="82419-155">SQL Server in an Azure virtual machine</span></span>| <span data-ttu-id="82419-156">База данных Azure для MySQL</span><span class="sxs-lookup"><span data-stu-id="82419-156">Azure Database for MySQL</span></span> | <span data-ttu-id="82419-157">База данных Azure для PostgreSQL</span><span class="sxs-lookup"><span data-stu-id="82419-157">Azure Database for PostgreSQL</span></span>|
| --- | --- | --- | --- | --- | --- |
| <span data-ttu-id="82419-158">Максимальный размер экземпляра базы данных</span><span class="sxs-lookup"><span data-stu-id="82419-158">Maximum database instance size</span></span> | [<span data-ttu-id="82419-159">4 ТБ</span><span class="sxs-lookup"><span data-stu-id="82419-159">4 TB</span></span>](/azure/sql-database/sql-database-resource-limits) | <span data-ttu-id="82419-160">256 ТБ</span><span class="sxs-lookup"><span data-stu-id="82419-160">256 TB</span></span> | [<span data-ttu-id="82419-161">1 ТБ</span><span class="sxs-lookup"><span data-stu-id="82419-161">1 TB</span></span>](/azure/mysql/concepts-limits) | [<span data-ttu-id="82419-162">1 ТБ</span><span class="sxs-lookup"><span data-stu-id="82419-162">1 TB</span></span>](/azure/postgresql/concepts-limits) |
| <span data-ttu-id="82419-163">Поддержка пулов емкости</span><span class="sxs-lookup"><span data-stu-id="82419-163">Supports capacity pools</span></span>  | <span data-ttu-id="82419-164">Yes</span><span class="sxs-lookup"><span data-stu-id="82419-164">Yes</span></span> | <span data-ttu-id="82419-165">Yes</span><span class="sxs-lookup"><span data-stu-id="82419-165">Yes</span></span> | <span data-ttu-id="82419-166">Нет </span><span class="sxs-lookup"><span data-stu-id="82419-166">No</span></span> | <span data-ttu-id="82419-167">Нет </span><span class="sxs-lookup"><span data-stu-id="82419-167">No</span></span> |
| <span data-ttu-id="82419-168">Поддержка масштабирования кластеров</span><span class="sxs-lookup"><span data-stu-id="82419-168">Supports clusters scale out</span></span>  | <span data-ttu-id="82419-169">Нет </span><span class="sxs-lookup"><span data-stu-id="82419-169">No</span></span> | <span data-ttu-id="82419-170">Yes</span><span class="sxs-lookup"><span data-stu-id="82419-170">Yes</span></span> | <span data-ttu-id="82419-171">Нет </span><span class="sxs-lookup"><span data-stu-id="82419-171">No</span></span> | <span data-ttu-id="82419-172">Нет </span><span class="sxs-lookup"><span data-stu-id="82419-172">No</span></span> |
| <span data-ttu-id="82419-173">Динамическая масштабируемость (увеличение масштаба)</span><span class="sxs-lookup"><span data-stu-id="82419-173">Dynamic scalability (scale up)</span></span>  | <span data-ttu-id="82419-174">Yes</span><span class="sxs-lookup"><span data-stu-id="82419-174">Yes</span></span> | <span data-ttu-id="82419-175">Нет </span><span class="sxs-lookup"><span data-stu-id="82419-175">No</span></span> | <span data-ttu-id="82419-176">Yes</span><span class="sxs-lookup"><span data-stu-id="82419-176">Yes</span></span> | <span data-ttu-id="82419-177">Yes</span><span class="sxs-lookup"><span data-stu-id="82419-177">Yes</span></span> |

### <a name="analytic-workload-capabilities"></a><span data-ttu-id="82419-178">Возможности для поддержки аналитических рабочих нагрузок</span><span class="sxs-lookup"><span data-stu-id="82419-178">Analytic workload capabilities</span></span>
| | <span data-ttu-id="82419-179">Базы данных SQL Azure</span><span class="sxs-lookup"><span data-stu-id="82419-179">Azure SQL Database</span></span> | <span data-ttu-id="82419-180">SQL Server на виртуальной машине Azure</span><span class="sxs-lookup"><span data-stu-id="82419-180">SQL Server in an Azure virtual machine</span></span>| <span data-ttu-id="82419-181">База данных Azure для MySQL</span><span class="sxs-lookup"><span data-stu-id="82419-181">Azure Database for MySQL</span></span> | <span data-ttu-id="82419-182">База данных Azure для PostgreSQL</span><span class="sxs-lookup"><span data-stu-id="82419-182">Azure Database for PostgreSQL</span></span>|
| --- | --- | --- | --- | --- | --- | 
| <span data-ttu-id="82419-183">Временные таблицы</span><span class="sxs-lookup"><span data-stu-id="82419-183">Temporal tables</span></span> | <span data-ttu-id="82419-184">Yes</span><span class="sxs-lookup"><span data-stu-id="82419-184">Yes</span></span> | <span data-ttu-id="82419-185">Yes</span><span class="sxs-lookup"><span data-stu-id="82419-185">Yes</span></span> | <span data-ttu-id="82419-186">Нет </span><span class="sxs-lookup"><span data-stu-id="82419-186">No</span></span> | <span data-ttu-id="82419-187">Нет </span><span class="sxs-lookup"><span data-stu-id="82419-187">No</span></span> |
| <span data-ttu-id="82419-188">Таблицы в памяти (оптимизированные для памяти)</span><span class="sxs-lookup"><span data-stu-id="82419-188">In-memory (memory-optimized) tables</span></span> | <span data-ttu-id="82419-189">Yes</span><span class="sxs-lookup"><span data-stu-id="82419-189">Yes</span></span> | <span data-ttu-id="82419-190">Yes</span><span class="sxs-lookup"><span data-stu-id="82419-190">Yes</span></span> | <span data-ttu-id="82419-191">Нет </span><span class="sxs-lookup"><span data-stu-id="82419-191">No</span></span> | <span data-ttu-id="82419-192">Нет </span><span class="sxs-lookup"><span data-stu-id="82419-192">No</span></span> |
| <span data-ttu-id="82419-193">Поддержка columnstore</span><span class="sxs-lookup"><span data-stu-id="82419-193">Columnstore support</span></span> | <span data-ttu-id="82419-194">Yes</span><span class="sxs-lookup"><span data-stu-id="82419-194">Yes</span></span> | <span data-ttu-id="82419-195">Yes</span><span class="sxs-lookup"><span data-stu-id="82419-195">Yes</span></span> | <span data-ttu-id="82419-196">Нет </span><span class="sxs-lookup"><span data-stu-id="82419-196">No</span></span> | <span data-ttu-id="82419-197">Нет </span><span class="sxs-lookup"><span data-stu-id="82419-197">No</span></span> |
| <span data-ttu-id="82419-198">Адаптивная обработка запросов</span><span class="sxs-lookup"><span data-stu-id="82419-198">Adaptive query processing</span></span> | <span data-ttu-id="82419-199">Yes</span><span class="sxs-lookup"><span data-stu-id="82419-199">Yes</span></span> | <span data-ttu-id="82419-200">Yes</span><span class="sxs-lookup"><span data-stu-id="82419-200">Yes</span></span> | <span data-ttu-id="82419-201">Нет </span><span class="sxs-lookup"><span data-stu-id="82419-201">No</span></span> | <span data-ttu-id="82419-202">Нет </span><span class="sxs-lookup"><span data-stu-id="82419-202">No</span></span> |

### <a name="availability-capabilities"></a><span data-ttu-id="82419-203">Возможности доступности</span><span class="sxs-lookup"><span data-stu-id="82419-203">Availability capabilities</span></span>
| | <span data-ttu-id="82419-204">Базы данных SQL Azure</span><span class="sxs-lookup"><span data-stu-id="82419-204">Azure SQL Database</span></span> | <span data-ttu-id="82419-205">SQL Server на виртуальной машине Azure</span><span class="sxs-lookup"><span data-stu-id="82419-205">SQL Server in an Azure virtual machine</span></span>| <span data-ttu-id="82419-206">База данных Azure для MySQL</span><span class="sxs-lookup"><span data-stu-id="82419-206">Azure Database for MySQL</span></span> | <span data-ttu-id="82419-207">База данных Azure для PostgreSQL</span><span class="sxs-lookup"><span data-stu-id="82419-207">Azure Database for PostgreSQL</span></span>|
| --- | --- | --- | --- | --- | --- | 
| <span data-ttu-id="82419-208">Вторичные реплики, доступные для чтения</span><span class="sxs-lookup"><span data-stu-id="82419-208">Readable secondaries</span></span> | <span data-ttu-id="82419-209">Yes</span><span class="sxs-lookup"><span data-stu-id="82419-209">Yes</span></span> | <span data-ttu-id="82419-210">Yes</span><span class="sxs-lookup"><span data-stu-id="82419-210">Yes</span></span> | <span data-ttu-id="82419-211">Нет </span><span class="sxs-lookup"><span data-stu-id="82419-211">No</span></span> | <span data-ttu-id="82419-212">Нет </span><span class="sxs-lookup"><span data-stu-id="82419-212">No</span></span> | 
| <span data-ttu-id="82419-213">Георепликация</span><span class="sxs-lookup"><span data-stu-id="82419-213">Geographic replication</span></span> | <span data-ttu-id="82419-214">Yes</span><span class="sxs-lookup"><span data-stu-id="82419-214">Yes</span></span> | <span data-ttu-id="82419-215">Yes</span><span class="sxs-lookup"><span data-stu-id="82419-215">Yes</span></span> | <span data-ttu-id="82419-216">Нет </span><span class="sxs-lookup"><span data-stu-id="82419-216">No</span></span> | <span data-ttu-id="82419-217">Нет </span><span class="sxs-lookup"><span data-stu-id="82419-217">No</span></span> | 
| <span data-ttu-id="82419-218">Автоматический переход на вторичный ресурс</span><span class="sxs-lookup"><span data-stu-id="82419-218">Automatic failover to secondary</span></span> | <span data-ttu-id="82419-219">Yes</span><span class="sxs-lookup"><span data-stu-id="82419-219">Yes</span></span> | <span data-ttu-id="82419-220">Нет </span><span class="sxs-lookup"><span data-stu-id="82419-220">No</span></span> | <span data-ttu-id="82419-221">Нет </span><span class="sxs-lookup"><span data-stu-id="82419-221">No</span></span> | <span data-ttu-id="82419-222">Нет </span><span class="sxs-lookup"><span data-stu-id="82419-222">No</span></span>|
| <span data-ttu-id="82419-223">Восстановление до точки во времени</span><span class="sxs-lookup"><span data-stu-id="82419-223">Point-in-time restore</span></span> | <span data-ttu-id="82419-224">Yes</span><span class="sxs-lookup"><span data-stu-id="82419-224">Yes</span></span> | <span data-ttu-id="82419-225">Yes</span><span class="sxs-lookup"><span data-stu-id="82419-225">Yes</span></span> | <span data-ttu-id="82419-226">Yes</span><span class="sxs-lookup"><span data-stu-id="82419-226">Yes</span></span> | <span data-ttu-id="82419-227">Yes</span><span class="sxs-lookup"><span data-stu-id="82419-227">Yes</span></span> |

### <a name="security-capabilities"></a><span data-ttu-id="82419-228">Возможности системы безопасности</span><span class="sxs-lookup"><span data-stu-id="82419-228">Security capabilities</span></span>
| | <span data-ttu-id="82419-229">Базы данных SQL Azure</span><span class="sxs-lookup"><span data-stu-id="82419-229">Azure SQL Database</span></span> | <span data-ttu-id="82419-230">SQL Server на виртуальной машине Azure</span><span class="sxs-lookup"><span data-stu-id="82419-230">SQL Server in an Azure virtual machine</span></span>| <span data-ttu-id="82419-231">База данных Azure для MySQL</span><span class="sxs-lookup"><span data-stu-id="82419-231">Azure Database for MySQL</span></span> | <span data-ttu-id="82419-232">База данных Azure для PostgreSQL</span><span class="sxs-lookup"><span data-stu-id="82419-232">Azure Database for PostgreSQL</span></span>|
| --- | --- | --- | --- | --- | --- | 
| <span data-ttu-id="82419-233">Безопасность на уровне строк</span><span class="sxs-lookup"><span data-stu-id="82419-233">Row level security</span></span> | <span data-ttu-id="82419-234">Yes</span><span class="sxs-lookup"><span data-stu-id="82419-234">Yes</span></span> | <span data-ttu-id="82419-235">Yes</span><span class="sxs-lookup"><span data-stu-id="82419-235">Yes</span></span> | <span data-ttu-id="82419-236">Yes</span><span class="sxs-lookup"><span data-stu-id="82419-236">Yes</span></span> | <span data-ttu-id="82419-237">Yes</span><span class="sxs-lookup"><span data-stu-id="82419-237">Yes</span></span> |
| <span data-ttu-id="82419-238">Маскирование данных</span><span class="sxs-lookup"><span data-stu-id="82419-238">Data masking</span></span> | <span data-ttu-id="82419-239">Yes</span><span class="sxs-lookup"><span data-stu-id="82419-239">Yes</span></span> | <span data-ttu-id="82419-240">Yes</span><span class="sxs-lookup"><span data-stu-id="82419-240">Yes</span></span> | <span data-ttu-id="82419-241">Нет </span><span class="sxs-lookup"><span data-stu-id="82419-241">No</span></span> | <span data-ttu-id="82419-242">Нет </span><span class="sxs-lookup"><span data-stu-id="82419-242">No</span></span> |
| <span data-ttu-id="82419-243">Прозрачное шифрование данных</span><span class="sxs-lookup"><span data-stu-id="82419-243">Transparent data encryption</span></span> | <span data-ttu-id="82419-244">Yes</span><span class="sxs-lookup"><span data-stu-id="82419-244">Yes</span></span> | <span data-ttu-id="82419-245">Yes</span><span class="sxs-lookup"><span data-stu-id="82419-245">Yes</span></span> | <span data-ttu-id="82419-246">Yes</span><span class="sxs-lookup"><span data-stu-id="82419-246">Yes</span></span> | <span data-ttu-id="82419-247">Yes</span><span class="sxs-lookup"><span data-stu-id="82419-247">Yes</span></span> |
| <span data-ttu-id="82419-248">Ограничение доступа для определенных IP-адресов</span><span class="sxs-lookup"><span data-stu-id="82419-248">Restrict access to specific IP addresses</span></span> | <span data-ttu-id="82419-249">Yes</span><span class="sxs-lookup"><span data-stu-id="82419-249">Yes</span></span> | <span data-ttu-id="82419-250">Yes</span><span class="sxs-lookup"><span data-stu-id="82419-250">Yes</span></span> | <span data-ttu-id="82419-251">Yes</span><span class="sxs-lookup"><span data-stu-id="82419-251">Yes</span></span> | <span data-ttu-id="82419-252">Yes</span><span class="sxs-lookup"><span data-stu-id="82419-252">Yes</span></span> |
| <span data-ttu-id="82419-253">Ограничение доступа для разрешения доступа только к виртуальной сети</span><span class="sxs-lookup"><span data-stu-id="82419-253">Restrict access to allow VNET access only</span></span> | <span data-ttu-id="82419-254">Yes</span><span class="sxs-lookup"><span data-stu-id="82419-254">Yes</span></span> | <span data-ttu-id="82419-255">Yes</span><span class="sxs-lookup"><span data-stu-id="82419-255">Yes</span></span> | <span data-ttu-id="82419-256">Нет </span><span class="sxs-lookup"><span data-stu-id="82419-256">No</span></span> | <span data-ttu-id="82419-257">Нет </span><span class="sxs-lookup"><span data-stu-id="82419-257">No</span></span> |
| <span data-ttu-id="82419-258">Аутентификация Azure Active Directory</span><span class="sxs-lookup"><span data-stu-id="82419-258">Azure Active Directory authentication</span></span> | <span data-ttu-id="82419-259">Yes</span><span class="sxs-lookup"><span data-stu-id="82419-259">Yes</span></span> | <span data-ttu-id="82419-260">Yes</span><span class="sxs-lookup"><span data-stu-id="82419-260">Yes</span></span> | <span data-ttu-id="82419-261">Нет </span><span class="sxs-lookup"><span data-stu-id="82419-261">No</span></span> | <span data-ttu-id="82419-262">Нет </span><span class="sxs-lookup"><span data-stu-id="82419-262">No</span></span> |
| <span data-ttu-id="82419-263">Аутентификация Active Directory</span><span class="sxs-lookup"><span data-stu-id="82419-263">Active Directory authentication</span></span> | <span data-ttu-id="82419-264">Нет </span><span class="sxs-lookup"><span data-stu-id="82419-264">No</span></span> | <span data-ttu-id="82419-265">Yes</span><span class="sxs-lookup"><span data-stu-id="82419-265">Yes</span></span> | <span data-ttu-id="82419-266">Нет </span><span class="sxs-lookup"><span data-stu-id="82419-266">No</span></span> | <span data-ttu-id="82419-267">Нет </span><span class="sxs-lookup"><span data-stu-id="82419-267">No</span></span> |
| <span data-ttu-id="82419-268">Многофакторная Идентификация</span><span class="sxs-lookup"><span data-stu-id="82419-268">Multi-factor authentication</span></span> | <span data-ttu-id="82419-269">Yes</span><span class="sxs-lookup"><span data-stu-id="82419-269">Yes</span></span> | <span data-ttu-id="82419-270">Yes</span><span class="sxs-lookup"><span data-stu-id="82419-270">Yes</span></span> | <span data-ttu-id="82419-271">Нет </span><span class="sxs-lookup"><span data-stu-id="82419-271">No</span></span> | <span data-ttu-id="82419-272">Нет </span><span class="sxs-lookup"><span data-stu-id="82419-272">No</span></span> |
| <span data-ttu-id="82419-273">Поддержка функции [Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine)</span><span class="sxs-lookup"><span data-stu-id="82419-273">Supports [Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine)</span></span> | <span data-ttu-id="82419-274">Yes</span><span class="sxs-lookup"><span data-stu-id="82419-274">Yes</span></span> | <span data-ttu-id="82419-275">Yes</span><span class="sxs-lookup"><span data-stu-id="82419-275">Yes</span></span> | <span data-ttu-id="82419-276">Yes</span><span class="sxs-lookup"><span data-stu-id="82419-276">Yes</span></span> | <span data-ttu-id="82419-277">Нет </span><span class="sxs-lookup"><span data-stu-id="82419-277">No</span></span> | <span data-ttu-id="82419-278">Нет </span><span class="sxs-lookup"><span data-stu-id="82419-278">No</span></span> |
| <span data-ttu-id="82419-279">Частный IP-адрес</span><span class="sxs-lookup"><span data-stu-id="82419-279">Private IP</span></span> | <span data-ttu-id="82419-280">Нет </span><span class="sxs-lookup"><span data-stu-id="82419-280">No</span></span> | <span data-ttu-id="82419-281">Yes</span><span class="sxs-lookup"><span data-stu-id="82419-281">Yes</span></span> | <span data-ttu-id="82419-282">Yes</span><span class="sxs-lookup"><span data-stu-id="82419-282">Yes</span></span> | <span data-ttu-id="82419-283">Нет </span><span class="sxs-lookup"><span data-stu-id="82419-283">No</span></span> | <span data-ttu-id="82419-284">Нет </span><span class="sxs-lookup"><span data-stu-id="82419-284">No</span></span> |
