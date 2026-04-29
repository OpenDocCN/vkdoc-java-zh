# 5. Google Cloud SQL

在本章中，我们将讨论如何使用 Google Cloud SQL 在基于云的 SQL 数据库中存储和管理数据。我们将介绍使用 Cloud SQL 的各个方面，例如创建 Cloud SQL 实例、连接到该实例、创建表，以及插入、更新和查询数据。我们还将讨论如何使用 Java 编程语言与 Cloud SQL 进行交互。

Google Cloud SQL 是 Google Cloud Platform (GCP) 提供的一种全托管关系型数据库服务，允许用户使用 MySQL、PostgreSQL 或 SQL Server 数据库引擎创建和管理数据库。它提供了一个基于 Web 的简单界面来管理数据库，并与 App Engine、Compute Engine 和 Cloud Storage 等其他 GCP 服务集成。

借助 Cloud SQL，用户可以轻松创建、配置和管理数据库，并享受自动备份、自动软件更新以及内置的监控和日志记录功能。用户还可以选择创建新数据库或导入现有数据库。

Cloud SQL 提供了多种功能来帮助用户管理其数据库，例如：

*   *自动备份*：Cloud SQL 会自动创建和管理数据库备份，以便您可以轻松地从数据丢失或损坏中恢复。

*   *自动软件更新*：Cloud SQL 会自动将数据库软件更新到最新版本，确保您的数据库始终拥有最新的安全性和性能增强功能。

*   *高可用性*：Cloud SQL 是 Google Cloud Platform 提供的一项服务，允许您在云中运行数据库。它就像一个数字仓库，以有序的方式存储信息。Cloud SQL 的优势之一在于，即使出现问题，它也能确保您的信息安全且可用。它通过在主副本出现问题时自动切换到数据库的备份副本来实现这一点。

*   *可伸缩性*：Cloud SQL 允许您根据需要轻松地向上或向下扩展数据库，且停机时间最短。

*   *安全性*：Cloud SQL 提供了多种安全功能来帮助保护您的数据库，例如静态和传输中加密，以及使用防火墙规则控制对数据库的访问。

*   *监控和日志记录*：Cloud SQL 提供内置的监控和日志记录功能，因此您可以轻松跟踪数据库的性能和活动。

*   *与其他 GCP 服务的集成*：Cloud SQL 可以轻松地与 App Engine、Compute Engine 和 Cloud Storage 等其他 GCP 服务集成，从而轻松为您的应用程序构建完整的基于云的解决方案。

对于寻求易于使用且易于与其他 GCP 服务集成的全托管、高性能关系型数据库服务的用户来说，Cloud SQL 是一个绝佳的选择。

## Cloud SQL 数据库的自动备份

自动备份是 Google Cloud SQL 的一项功能，允许该服务自动创建和管理数据库备份。这些备份存储在单独的存储位置，可用于从数据丢失或损坏中恢复。

备份的频率和保留期可由用户配置。例如，您可以设置每天、每周或每月进行备份，最多可保留 365 个备份。您还可以设置备份的保留期，这意味着超过您设置的保留期的备份将被自动删除。

Cloud SQL 备份是增量备份，这意味着新备份仅包含自上次备份以来对数据库所做的更改。这使得备份过程更高效，并降低了存储成本。这意味着 Cloud SQL 会对您的数据库实例进行一次完整备份，然后使用增量备份仅捕获自上次备份以来所做的更改。这意味着第一个备份包含数据库中的所有数据，但后续备份仅捕获新的或更改的数据，而不是在每个备份中复制所有数据。

这些备份可用于恢复整个数据库或特定表，也可用于创建与原始数据库具有相同数据的新 Cloud SQL 实例。

Google Cloud SQL 中的自动备份功能允许用户自动创建和管理数据库备份，并具有可配置的频率和保留期。这些备份可用于从数据丢失或损坏中恢复，也可用于创建与原始数据库具有相同数据的新 Cloud SQL 实例。

现在我们将讨论配置自动备份。

### 配置自动备份

要为 Cloud SQL 实例配置自动备份，您可以按照以下步骤操作：

1.  转到 Google Cloud Console 中的 Cloud SQL 实例页面。

2.  单击要为其配置自动备份的实例。

3.  在实例详细信息页面上，单击“备份”选项卡。

4.  在“自动备份”部分，您可以配置以下选项：
    *   *备份窗口*：执行备份的时间窗口。您可以选择预定义的窗口或指定自定义窗口。

    *   *保留期*：备份将被保留的天数。达到保留期后，备份将被自动删除。

5.  单击“保存”以应用更改。

需要注意的是，备份仅会在您指定的备份窗口内执行，并且仅当实例在该窗口期间处于运行状态时才会执行。

您还可以在“备份”选项卡上查看备份状态并下载备份。

注意

当您创建新的 Cloud SQL 实例时，默认启用自动备份，保留期设置为 7 天。

## 数据库软件自动更新到最新版本

Google Cloud SQL 的自动软件更新功能允许该服务自动将数据库软件更新到最新版本。这确保了您的数据库始终拥有最新的安全性和性能增强功能。

当数据库软件的新版本发布时，Cloud SQL 会安排一个更新窗口，在此期间应用更新。用户可以自定义更新窗口。更新过程设计为无中断，这意味着在更新过程中您的数据库将继续可用。

需要注意的是，自动软件更新仅适用于数据库软件，而不适用于数据库中存储的数据。此外，在将更新应用于生产数据库之前，最好先在非生产环境中进行测试。

要为 Cloud SQL 实例配置自动软件更新，您可以按照以下步骤操作：

1.  转到 Google Cloud Console 中的 Cloud SQL 实例页面。

2.  单击要为其配置自动更新的实例。

3.  在实例详细信息页面上，单击“设置”选项卡。

4.  在“自动更新”部分，您可以选择“自动修补次要版本升级”选项。

5.  单击“保存”以应用更改。

需要注意的是，自动软件更新仅适用于次要版本更新，而不适用于主要版本更新。对于主要版本更新，用户将收到通知，并且必须手动执行更新。

简而言之，自动软件更新是 Google Cloud SQL 中的一项功能，允许该服务自动将数据库软件更新到最新版本，确保数据库始终拥有最新的安全性和性能增强功能。此功能可以在 Cloud SQL 实例中进行配置，并且是无中断的，这意味着在更新过程中数据库将继续可用。



## Google Cloud SQL 的高可用性

高可用性（HA）是一项确保系统或服务能够持续运行并可供用户使用的功能，即使在计划内或计划外中断的情况下也是如此。在 Google Cloud SQL 的语境中，高可用性指的是该服务在发生意外故障或维护时，仍能保持数据库运行和可访问的能力。

Google Cloud SQL 提供了多种实现高可用性的选项。

*   *只读副本*：此功能允许您创建一个或多个主数据库的只读副本。这些副本会与主数据库实时同步更新，可用于分流主数据库的读取流量。这有助于提升性能并降低主数据库过载的风险。

*   *自动故障转移*：如果主实例发生故障，此功能会自动切换到备用副本。这确保了即使在意外故障的情况下，服务也能保持可用。为了实现高可用性，您可以在不同区域设置只读副本，并在整个区域宕机时进行故障转移。

*   *多可用区部署*：此功能允许您将 Cloud SQL 实例部署在一个区域内的多个可用区中。这提供了针对因区域特定问题（如断电或网络故障）导致的故障和中断的额外保护。

*   *Cloud SQL for PostgreSQL 和 MySQL 支持复制*：这意味着您可以使用复制来创建高可用性设置。

*   *Cloud SQL for PostgreSQL 和 MySQL 支持 Cloud SQL 托管复制*：这意味着您可以使用复制并由 Cloud SQL 服务进行管理，以创建高可用性设置。

值得注意的是，其中一些选项可能会产生额外费用。在决定为您的 Cloud SQL 实例采用哪种高可用性解决方案之前，务必评估每个选项的具体要求和成本影响。

Google Cloud SQL 提供了多种实现高可用性的选项，例如只读副本、自动故障转移、多可用区部署以及对 PostgreSQL 和 MySQL 的复制支持。这些选项有助于提升性能并降低主数据库过载的风险，确保即使在意外故障或维护期间也能保证服务可用性。

下一节我们将详细讨论只读副本。

## Cloud SQL 只读副本

只读副本是 Google Cloud SQL 的一项功能，允许您创建一个或多个主数据库的只读副本。这些副本会与主数据库实时同步更新，可用于分流主数据库的读取流量。这有助于提升性能并降低主数据库过载的风险。

创建只读副本时，它会首先复制主数据库中的数据。之后，它会通过复制过程持续接收来自主数据库的更新。这确保了副本始终拥有最新的数据。

只读副本可用于多种目的，例如：

*   *扩展读取性能*：通过将读取流量分流到副本，您可以提升数据库的整体性能。这对于读取密集型工作负载或为不同地理位置的用户提供服务特别有用。

*   *分析工作负载*：通过拥有只读副本，您可以创建一个专门用于分析工作负载的独立副本；这将有助于避免影响主副本的性能。

*   *备份*：只读副本可用作创建备份的数据源。

*   *多区域部署*：通过在不同区域拥有只读副本，您可以为不同地理位置的用户提供更低的延迟服务。

Cloud SQL for MySQL 和 Cloud SQL for PostgreSQL 均支持只读副本。

需要注意的是，虽然只读副本可用于分流读取流量，但不能用于分流写入流量。写入操作必须指向主数据库。此外，务必评估创建只读副本的成本影响，因为每个副本实例都会产生额外费用。

这些副本可用于多种目的，例如扩展读取性能、分析工作负载、备份和多区域部署。Cloud SQL for MySQL 和 Cloud SQL for PostgreSQL 均支持只读副本，但务必评估创建只读副本的成本影响，因为每个副本实例都会产生额外费用。

接下来，我们将讨论 Cloud SQL 的自动故障转移。

## Cloud SQL：自动故障转移

自动故障转移是 Google Cloud SQL 的一项功能，如果主实例发生故障，它会自动切换到备用副本。这确保了即使在意外故障的情况下，服务也能保持可用。

当主实例发生故障时，系统会自动切换到一个健康的副本，该副本将成为新的主实例。此过程对用户和应用程序是透明的，不应中断服务的可用性。

对于启用了复制功能的 Cloud SQL for MySQL 和 Cloud SQL for PostgreSQL 实例，均支持自动故障转移。

当您创建新的 Cloud SQL 实例时，可以指定是否要启用自动故障转移。如果您选择启用自动故障转移，还可以指定要创建的副本数量，以及创建副本的可用区或区域。

值得注意的是，自动故障转移会产生额外费用。在决定为您的 Cloud SQL 实例启用此功能之前，务必评估其具体要求和成本影响。

对于启用了复制功能的 Cloud SQL for MySQL 和 Cloud SQL for PostgreSQL 实例，均支持自动故障转移。当您创建新的 Cloud SQL 实例时，可以指定是否要启用自动故障转移以及要创建的副本数量。然而，在决定为您的 Cloud SQL 实例启用此功能之前，务必评估其具体要求和成本影响。

### 多可用区部署

多可用区部署是 Google Cloud SQL 的一项功能，允许您在一个区域内的多个可用区中创建主数据库的副本。这通过提供针对可用区级别故障的保护，提高了数据库的可用性和持久性。

当您创建 Cloud SQL 实例时，可以指定要在其中创建副本的可用区数量。Google Cloud SQL 会自动在不同可用区中创建副本，并使其与主数据库保持同步。

如果主实例发生故障，位于不同可用区中的一个副本将自动提升为主实例并接管工作。此过程对用户和应用程序是透明的，不应中断服务的可用性。

Cloud SQL for MySQL 和 Cloud SQL for PostgreSQL 实例均支持多可用区部署。

值得注意的是，使用多个可用区进行复制会增加运行 Cloud SQL 实例的成本。在决定为您的 Cloud SQL 实例启用此功能之前，务必评估其具体要求和成本影响。

此功能适用于 Cloud SQL for MySQL 和 Cloud SQL for PostgreSQL 实例。在创建新的 Cloud SQL 实例时，您可以指定要在其中创建副本的可用区数量。然而，在决定为您的 Cloud SQL 实例启用此功能之前，务必评估其具体要求和成本影响。



## Cloud SQL：可伸缩性

可伸缩性是指系统（例如数据库）处理不断增加的工作量或负载的能力。Google Cloud SQL 提供了多种根据您的需求扩展数据库的选项。

扩展 Cloud SQL 实例的一种方法是增加其内存和 CPU 资源。这可以通过修改实例的机器类型来实现。这使您能够处理更多并发连接并执行更复杂的查询。

扩展 Cloud SQL 实例的另一种方法是创建只读副本。只读副本是主实例的副本，可用于处理只读工作负载，例如读取密集型工作负载。这使您可以将读取流量从主实例分流，从而提高性能。

此外，Google Cloud SQL 允许您通过将数据库分片到多个实例来进行横向扩展。这是一种更高级的技术，但它允许您水平扩展数据库并处理非常大的工作负载。

Google Cloud SQL 提供了多种根据您的需求扩展数据库的选项，例如增加其内存和 CPU 资源、创建只读副本，或将其分片到多个实例。您可以修改实例的机器类型以处理更多并发连接和复杂查询，为读取密集型工作负载创建只读副本，将读取流量从主实例分流，并为非常大的工作负载实施分片。

## Cloud SQL：安全性

要保护您的 Google Cloud SQL 实例，您可以采取以下几个步骤。

1.  使用 Cloud SQL 身份验证来控制对您实例的访问。此功能允许您使用 Google Cloud 身份与访问管理 (IAM) 来授予或拒绝对特定用户或组的访问权限。

2.  使用 Cloud SQL 授权来控制对您实例内特定数据库和表的访问。

3.  为您的实例启用加密，以保护静态数据和传输中的数据。Cloud SQL 支持客户管理的加密密钥和 Google 管理的加密密钥。

4.  使用 Cloud SQL 私有 IP 从您的 Google Cloud Virtual Private Cloud (VPC) 网络内部安全且私密地连接到您的实例。

5.  使用 Cloud SQL 备份和复制来确保在发生灾难时您的数据受到保护且可用。

6.  定期检查 Cloud SQL 审计日志，以监控异常活动并确保符合安全策略。

要配置安全设置，您可以使用 Cloud SQL 网页界面、`gcloud` 命令行工具或 Cloud SQL API。您需要拥有适当的权限才能为您的实例配置安全设置。

安全是一个持续的过程，您需要定期审查和更新您的安全配置，以确保它们满足您的需求并保护您的数据。

### 身份验证

Cloud SQL 身份验证是 Google Cloud SQL 的一项功能，允许您使用 Google Cloud IAM 权限来控制对您实例的访问。

使用 Cloud SQL 身份验证，您可以根据用户的 Google Cloud Platform 身份来授予或拒绝对特定用户或组的访问权限。这意味着您可以使用与其他 GCP 资源相同的身份验证和授权机制来控制对 Cloud SQL 实例的访问。

您可以使用两种类型的身份验证方法与 Cloud SQL 配合使用。

*   *Cloud IAM 身份验证*：此方法使用 Google Cloud IAM 根据用户的 GCP 身份来授予或拒绝对特定用户或组的访问权限。

*   *数据库身份验证*：此方法使用底层数据库引擎提供的标准身份验证机制。例如，您可以使用用户名和密码身份验证来授予或拒绝对特定用户的访问权限。

您可以使用 Cloud SQL 网页界面、`gcloud` 命令行工具或 Cloud SQL API 为 Cloud SQL 实例配置身份验证设置。

要使用 Cloud SQL 身份验证，您需要启用 Cloud SQL API，创建一个具有适当权限的服务账号，并配置您的 Cloud SQL 实例以使用该服务账号。

### 授权

Cloud SQL 授权指的是控制对 Cloud SQL 实例内特定数据库和表的访问过程。这允许您指定哪些用户或组有权对您的实例执行特定操作，例如 `SELECT`、`INSERT`、`UPDATE` 或 `DELETE`。

Cloud SQL 支持两种类型的授权方法。

*   *Cloud IAM 授权*：此方法使用 Google Cloud IAM 根据用户的 GCP 身份来授予或拒绝对特定用户或组的访问权限。您可以指定每个用户或组对特定 Cloud SQL 实例或特定数据库/表拥有的权限。

*   *数据库授权*：此方法使用底层数据库引擎提供的标准授权机制。例如，您可以在 MySQL 中创建用户和角色，并授予或撤销他们的特定权限。

您可以使用 Cloud SQL 网页界面、`gcloud` 命令行工具或 Cloud SQL API 为 Cloud SQL 实例配置授权设置。

授权与身份验证协同工作，最佳实践是将这两种方法结合使用，以确保只有授权用户才能访问您的 Cloud SQL 实例，并对其中的数据库和表执行特定操作。

### 加密

加密是将明文转换为密文以保护数据免遭未经授权访问的过程。Google Cloud SQL 支持加密以保护静态数据和传输中的数据。

**静态数据加密：**

*   Google Cloud SQL 支持客户管理的加密密钥 (CMEK) 和 Google 管理的加密密钥 (GMEK) 来加密存储在磁盘上的数据。
*   使用 CMEK，您可以管理自己的加密密钥，而使用 GMEK，则由 Google 为您处理密钥管理。
*   两种方法都使用具有 256 位密钥的高级加密标准 (AES) 算法来加密数据。

**传输中数据加密：**

*   Cloud SQL 支持传输层安全性 (TLS) 来加密客户端和 Cloud SQL 实例之间传输中的数据。
*   您可以配置实例的加密设置，并使用 SSL/TLS 证书来建立安全连接。

请务必记住，加密并不能保护您免受因配置错误（例如弱密码）导致的安全漏洞的影响，因此，将加密与其他安全措施结合使用以确保数据的整体安全性非常重要。

您可以使用 Cloud SQL 网页界面、`gcloud` 命令行工具或 Cloud SQL API 为 Cloud SQL 实例配置加密设置。



### 私有 IP

Cloud SQL 私有 IP 是 Google Cloud SQL 的一项功能，允许您从 Google Cloud VPC 网络内部安全且私密地连接到您的实例。使用私有 IP 后，您的 Cloud SQL 实例会被分配一个 VPC 网络内的私有 IP 地址，这意味着与实例的连接是通过内部网络进行的，而非互联网。借助 Cloud SQL 私有 IP，数据将保留在 Google 的网络内，不会暴露于公共互联网。这为您的数据提供了额外的安全层，因为数据不会通过可能被未经授权方截获的互联网进行传输。

通过使用 Cloud SQL 私有 IP，您可以执行以下操作：

*   无需在防火墙上开放入站端口，从而增强连接的安全性。
*   通过减少延迟和提高吞吐量来改善连接性能。
*   无需 VPN 或其他网络级安全机制，从而简化网络架构。

要使用 Cloud SQL 私有 IP，您需要执行以下操作：

*   创建一个 VPC 网络及该网络内的一个子网。
*   创建一个使用相同 VPC 网络和子网的 Cloud SQL 实例。
*   使用其私有 IP 地址连接到该 Cloud SQL 实例。

您可以将 Cloud SQL 私有 IP 与 Cloud SQL 的身份验证、授权和加密功能结合使用，从而为您的 Cloud SQL 实例创建高度安全且高性能的连接。

### 审计日志

审计日志是记录系统内发生的事件（例如用户操作、系统事件和安全相关活动）的过程。Google Cloud SQL 支持审计日志，以帮助您监控和分析实例上的活动。

Cloud SQL 审计日志提供有关实例活动的详细信息，例如：

*   谁执行了特定查询
*   执行了什么查询
*   何时执行的
*   从何处执行的

您可以在 Cloud 控制台的 Cloud SQL 日志查看器中查看审计日志，或将其导出到 Cloud Logging 或 BigQuery 以进行进一步分析。

要为 Cloud SQL 实例启用审计日志，您需要遵循以下步骤：

1.  创建一个 Cloud SQL 实例。
2.  启用 Cloud SQL Admin API。
3.  创建一个 Cloud Logging 接收器或 BigQuery 数据集。
4.  配置 Cloud SQL 实例以将日志导出到 Cloud Logging 接收器或 BigQuery 数据集。

审计日志可以帮助您检测和排查问题、检测和防止安全漏洞，并满足法规要求。最佳实践是定期审查审计日志，并根据其提供的信息采取相应措施。

## Cloud SQL：监控与日志记录

Google Cloud SQL 监控与日志记录是重要的工具，可帮助您了解 Cloud SQL 实例的性能和活动，并检测和排查问题。

### 监控

以下是监控功能：

*   Cloud SQL 监控允许您跟踪实例的性能，例如 CPU 和内存使用率、连接数以及磁盘空间使用情况。
*   您可以使用 Cloud SQL 监控来设置提醒，以便在达到特定阈值（例如 CPU 使用率超过某个百分比）时收到通知。
*   您可以在 Cloud 控制台中查看监控数据，或将其导出到 Cloud Monitoring 以进行进一步分析。
*   要为 Cloud SQL 实例启用监控，您需要执行以下操作：
    1.  创建一个 Cloud SQL 实例。
    2.  启用 Cloud Monitoring API。
    3.  创建一个 Cloud 工作区。
    4.  将 Cloud SQL 实例链接到该 Cloud 工作区。

### 日志记录

以下是日志记录功能：

*   Cloud SQL 日志记录允许您记录实例内发生的事件，例如查询、错误和系统事件。
*   您可以使用 Cloud SQL 日志记录来跟踪用户活动、检测和排查问题，并满足法规要求。
*   您可以在 Cloud 控制台中查看日志，或将其导出到 Cloud Logging 或 BigQuery 以进行进一步分析。
*   要为 Cloud SQL 实例启用日志记录，您需要执行以下操作：
    1.  创建一个 Cloud SQL 实例。
    2.  启用 Cloud SQL Admin API。
    3.  创建一个 Cloud Logging 接收器或 BigQuery 数据集。
    4.  配置 Cloud SQL 实例以将日志导出到 Cloud Logging 接收器或 BigQuery 数据集。

监控和日志记录对于确保 Cloud SQL 实例的性能和安全性都至关重要，作为最佳实践，应配置并定期审查。

现在，我们来讨论如何将 Cloud SQL 与其他 Google Cloud 服务集成。

## Cloud SQL 与其他 GCP 服务的集成

Google Cloud SQL 可以与其他 Google Cloud Platform 服务集成，以创建更强大、更灵活的基于云的基础设施。以下是一些 Cloud SQL 如何与其他 GCP 服务集成的示例：

*   *Cloud Storage*：您可以使用 Cloud Storage 来存储备份和二进制日志，这些日志可用于在发生故障或数据丢失时恢复您的 Cloud SQL 实例。
*   *Cloud Load Balancing*：您可以使用 Cloud Load Balancing 将传入流量分发到您的 Cloud SQL 实例，这有助于提高性能和可用性。
*   *Cloud IAM*：您可以使用 Cloud IAM 来控制对 Cloud SQL 实例的访问，并为不同的用户和组精细调整权限。
*   *Cloud 数据丢失防护 (DLP)*：您可以使用 Cloud DLP 自动发现、分类和编辑 Cloud SQL 实例中的敏感数据，这有助于保护敏感信息并满足法规要求。
*   *Cloud Dataflow*：您可以使用 Cloud Dataflow 来处理和分析 Cloud SQL 实例中的数据，这有助于您获取洞察并做出数据驱动的决策。
*   *Cloud App Engine*：您可以使用 Cloud App Engine 来托管您的 Web 应用程序，并将其连接到您的 Cloud SQL 实例，这有助于您创建强大且可扩展的 Web 应用程序。



### Cloud SQL 与 Cloud Storage 的集成

Google Cloud SQL 可与 Google Cloud Storage 集成，用于存储备份和二进制日志，以便在发生故障或数据丢失时恢复您的 Cloud SQL 实例。这种集成使您能够确保数据安全且可恢复，即使在灾难情况下也是如此。

以下是一个如何将 Cloud SQL 与 Cloud Storage 集成的示例：

1.  创建一个 Cloud SQL 实例和一个 Cloud Storage 存储桶。

2.  在 Cloud SQL 实例中，转到“备份”标签页并设置备份配置。您可以选择按天、周或月进行备份，并指定保留期限。

3.  在“备份”标签页上，选择要存储备份的 Cloud Storage 存储桶。您还可以选择对备份进行加密以增强安全性。

4.  配置好备份后，您可以转到 Cloud SQL 实例中的“复制”标签页，并设置复制到另一个区域或另一个 Cloud SQL 实例。

5.  您还可以在 Cloud SQL 实例中启用二进制日志记录，这将允许您创建时间点恢复快照。

6.  设置复制到另一个区域或另一个 Cloud SQL 实例。

7.  启用二进制日志记录后，您可以创建一个 Cloud Storage 存储桶来存储二进制日志。

8.  转到 Cloud SQL 实例中的“二进制日志”标签页，选择要存储二进制日志的 Cloud Storage 存储桶，并设置保留期限。

9.  将二进制日志存储在 Cloud Storage 后，您可以使用它们将 Cloud SQL 实例恢复到特定时间点。

通过将 Cloud SQL 与 Cloud Storage 集成，您可以确保数据安全且可恢复，即使在灾难情况下也是如此。这种集成有助于提高 Cloud SQL 实例的可用性和弹性，并保护您的数据免遭丢失或损坏。

上述步骤可能会根据您使用的 Cloud SQL 版本而有所变化或不同。建议始终查阅官方文档以获取最新说明。

### Cloud SQL 与 Cloud Load Balancing 的集成

Google Cloud SQL 可与 Google Cloud Load Balancing 集成，以将传入流量分发到您的 Cloud SQL 实例，这有助于提高性能和可用性。这种集成使您能够创建高可用且可扩展的 Cloud SQL 基础设施，以处理大量并发连接。

以下是一个如何将 Cloud SQL 与 Cloud Load Balancing 集成的示例：

1.  创建一个 Cloud SQL 实例和一个 Cloud Load Balancing 实例。

2.  在 Cloud SQL 实例中，转到“连接”标签页，并设置最大连接数。这将帮助您控制任何给定时间可以连接到 Cloud SQL 实例的连接数。

3.  在 Cloud Load Balancing 实例中，创建一个新的后端服务，并将其指向 Cloud SQL 实例。

4.  创建一个新的负载均衡器，并将后端服务附加到其上。

5.  创建负载均衡器后，您可以配置健康检查和自动故障转移，以确保 Cloud SQL 实例始终可用。

6.  根据您的特定需求，配置负载均衡器以将传入流量分发到 Cloud SQL 实例。

7.  配置好负载均衡器后，您可以使用它将传入流量分发到 Cloud SQL 实例，并确保 Cloud SQL 实例始终可用，即使在发生故障时也是如此。

通过将 Cloud SQL 与 Cloud Load Balancing 集成，您可以创建高可用且可扩展的 Cloud SQL 基础设施，以处理大量并发连接。这种集成有助于提高 Cloud SQL 实例的性能和可用性，并确保您的用户始终可以访问您的数据。

上述步骤可能会根据您使用的 Cloud SQL 版本而有所变化或不同。建议始终查阅官方文档以获取最新说明。

### Cloud SQL 与 IAM 的集成

Google Cloud SQL 可与 Google Cloud IAM 集成，以控制对您的 Cloud SQL 实例和资源的访问。这种集成允许您根据组织的安全策略，授予或拒绝对特定用户、组或服务的访问权限。

以下是一个如何将 Cloud SQL 与 IAM 集成的示例：

1.  在 Google Cloud Console 中创建一个 Cloud SQL 实例和一个项目。

2.  在 Cloud SQL 实例中，转到“访问控制”标签页并创建一个新的 IAM 策略。

3.  在 IAM 策略中，指定应授予或拒绝特定用户、组或服务的角色和权限。例如，您可以将 Cloud SQL Editor 角色授予特定用户组，或拒绝对特定服务账号访问 Cloud SQL 实例。

4.  创建 IAM 策略后，您可以将其与 Cloud SQL 实例关联。

5.  在 Cloud SQL 实例中，转到“访问控制”标签页，并将 IAM 策略添加到 Cloud SQL 实例。

6.  将 IAM 策略与 Cloud SQL 实例关联后，将为指定的用户、组或服务强制执行指定的角色和权限。

通过将 Cloud SQL 与 IAM 集成，您可以根据组织的安全策略控制对 Cloud SQL 实例和资源的访问。这种集成有助于提高 Cloud SQL 实例的安全性，并确保只有授权的用户、组或服务才能访问您的数据。

上述步骤可能会根据您使用的 Cloud SQL 版本而有所变化或不同。建议始终查阅官方文档以获取最新说明。

### Cloud SQL 与 Cloud Data Loss Prevention 的集成

Google Cloud SQL 可与 Google Cloud DLP 集成，以保护 Cloud SQL 数据库中的敏感数据。这种集成允许您根据组织的安全策略，自动发现、分类和编辑 Cloud SQL 数据库中的敏感数据。由于监管要求，这也是必要的。

以下是一个如何将 Cloud SQL 与 Cloud DLP 集成的示例：

1.  在 Google Cloud Console 中创建一个 Cloud SQL 实例和一个项目。

2.  在 Cloud SQL 实例中，转到“安全”标签页，然后点击“数据丢失防护”。

3.  点击“创建新的 DLP 作业”，并将其配置为扫描 Cloud SQL 实例以查找敏感数据。

4.  在 DLP 作业配置中，指定要扫描的数据类型（例如，信用卡号、社会安全号码等），并指定如果检测到敏感数据，您希望 DLP 执行的操作（例如，编辑、掩码等）。

5.  配置好 DLP 作业后，您可以运行它并在 Cloud DLP 日志中查看结果。

6.  您还可以设置对 Cloud SQL 数据库的定期扫描甚至实时扫描。

通过将 Cloud SQL 与 Cloud DLP 集成，您可以根据组织的安全策略保护 Cloud SQL 数据库中的敏感数据。这种集成有助于提高 Cloud SQL 实例的安全性，并确保敏感数据免遭丢失或泄露。

上述步骤可能会根据您使用的 Cloud SQL 版本而有所变化或不同。建议始终查阅官方文档以获取最新说明。



### Cloud SQL 与 Cloud Dataflow 的集成

Google Cloud SQL 可与 Google Cloud Dataflow 集成，用于处理和分析 Cloud SQL 数据库中的数据。这种集成允许您使用 Dataflow 从 Cloud SQL 数据库中提取、转换和加载（ETL）数据，启用持续流式处理，然后利用 Dataflow 强大的数据处理能力对数据进行分析。

Cloud SQL 与 Dataflow 集成的一个用例场景是：某公司希望分析存储在 Cloud SQL 数据库中的客户数据，以深入了解客户行为和偏好。该公司可以使用 Dataflow 从 Cloud SQL 数据库中提取并转换相关数据，然后利用 Dataflow 强大的数据处理能力对数据进行分析，例如识别客户行为中的趋势和模式。这有助于公司对其产品和服务做出数据驱动的决策，并更好地理解客户的需求和偏好。

以下是一个如何将 Cloud SQL 与 Cloud Dataflow 集成的示例：

1.  在 Google Cloud Console 中创建一个 Cloud SQL 实例和一个项目。
2.  在 Google Cloud Console 中创建一个 Dataflow 作业，并将 Cloud SQL 实例指定为数据源。
3.  在 Dataflow 作业中，指定要在 Cloud SQL 实例上运行的 SQL 查询以提取数据。
4.  使用 Dataflow SDK 定义将处理和分析数据的管道。
5.  管道定义完成后，即可运行 Dataflow 作业来处理和分析数据。
6.  处理后的数据可以存储在 BigQuery 表、Cloud Storage 或您选择的其他存储选项中。

通过将 Cloud SQL 与 Cloud Dataflow 集成，您可以使用 Dataflow 强大的数据处理能力来处理和分析 Cloud SQL 数据库中的数据。这种集成可以帮助您从数据中获取洞察，并做出更好的数据驱动决策。它还允许更灵活地处理数据，并能轻松处理大量数据。

上述步骤可能会根据您使用的 Cloud SQL 版本而有所变化或不同。建议始终查阅官方文档以获取最新说明。

### Cloud SQL 与 Cloud App Engine 的集成

Google Cloud SQL 可与 Google Cloud App Engine 集成，用于创建使用 Cloud SQL 中存储数据的 Web 和移动应用程序。

以下是一个如何将 Cloud SQL 与 Cloud App Engine 集成的示例：

1.  在 Google Cloud Console 中创建一个 Cloud SQL 实例和一个项目。
2.  在同一项目中创建一个 App Engine 应用程序，并选择运行时环境（例如 Java、Python、Go 等）。
3.  在 App Engine 应用程序中，使用适用于该运行时环境的 Cloud SQL 库连接到 Cloud SQL 实例，并与实例中存储的数据进行交互。
4.  使用 Cloud SQL 库执行读取、写入、更新和删除 Cloud SQL 实例中数据等操作。
5.  一旦 App Engine 应用程序配置为与 Cloud SQL 实例交互，您就可以将其部署到 App Engine，用户将通过一个 URL 访问它。

通过将 Cloud SQL 与 Cloud App Engine 集成，您可以创建使用 Cloud SQL 中存储数据的 Web 和移动应用程序。这种集成使您能够轻松构建和部署可以读取、写入、更新和删除 Cloud SQL 实例中存储数据的应用程序。此外，App Engine 提供自动扩缩、负载均衡和监控服务，可帮助您创建高可用性和可扩展的应用程序。

上述步骤可能会根据您使用的 Cloud SQL 版本和运行时环境而有所变化或不同。建议始终查阅官方文档以获取最新说明。

## 了解 GCP 中的 SQL/NoSQL 选项

Google Cloud Platform 提供了多种用于处理 SQL 数据库的选项。

*   *Cloud SQL*：一种全托管的关系型数据库服务，允许您创建和管理 SQL 数据库，包括 MySQL 和 PostgreSQL。
*   *Cloud Spanner*：一种全球分布式的关系型数据库服务，提供类似 SQL 的接口，并具有自动同步复制功能以实现高可用性。
*   *Cloud Bigtable*：一种 NoSQL 宽列存储数据库，专为大规模、低延迟的数据处理而设计。
*   *Cloud Datastore*：一种 NoSQL 文档数据库，允许您使用简单的键值模型存储、检索和查询数据。
*   *BigQuery*：一种全托管的云原生数据仓库，允许您对大型数据集运行类似 SQL 的查询。

GCP 提供了一系列 SQL 和 NoSQL 数据库选项，以满足不同的用例和工作负载。Cloud SQL 是一种全托管的 SQL 数据库服务，而 Cloud Spanner 提供具有自动复制功能的全球分布式 SQL 数据库。Cloud Bigtable 是一种专为低延迟数据处理而设计的 NoSQL 宽列存储数据库，而 Cloud Datastore 是一种基于文档的 NoSQL 数据库，采用简单的键值模型。最后，BigQuery 是一种云原生数据仓库，允许对大型数据集进行类似 SQL 的查询。

在 GCP 中选择 SQL 选项时，务必考虑您用例的具体要求，例如需要存储的数据类型、应用程序的性能和可扩展性需求，以及您所需的控制和管理级别。

### Cloud SQL 相对于自管理本地数据库的优势

Cloud SQL 是 Google Cloud Platform 提供的一种全托管关系型数据库服务，允许您创建和管理 SQL 数据库，包括 MySQL 和 PostgreSQL。与自管理的本地数据库相比，它具有多项优势。

*   *可扩展性*：Cloud SQL 允许您轻松地向上或向下扩展数据库，以匹配应用程序的需求，而无需担心底层基础设施。
*   *高可用性*：Cloud SQL 自动跨多个可用区复制数据，以确保您的数据始终可用，即使在某个可用区发生故障时也是如此。
*   *自动备份*：Cloud SQL 自动每日备份您的数据，因此您无需担心手动备份数据库。
*   *安全性*：Cloud SQL 提供多项安全功能，例如静态和传输中加密，以确保您的数据受到保护。
*   *低维护*：使用 Cloud SQL，您无需担心管理和维护底层基础设施（如服务器和存储）。Google 负责处理所有底层基础设施，因此您可以专注于开发和部署应用程序。
*   *成本效益*：Cloud SQL 可能比运行自管理数据库更具成本效益，尤其是在工作负载可变的情况下。您只需为使用的资源付费，并且可以根据需要轻松扩展或缩减。
*   *集成*：Cloud SQL 可轻松与其他 GCP 服务（如 Cloud App Engine、Cloud Dataflow 和 BigQuery）集成，从而轻松构建和部署使用 Cloud SQL 中存储数据的应用程序。
*   *灵活性*：Cloud SQL 支持不同类型的 SQL 数据库，允许您选择最符合需求的数据库。

对于希望获得关系型数据库的优势，同时又不想承担管理和扩展基础设施的运营开销和成本的组织来说，Cloud SQL 是一个绝佳的选择。



### Cloud SQL 的用例

Cloud SQL 有多种使用场景。

*   *传统关系型数据库*：Cloud SQL 是在完全托管的环境中运行传统关系型数据库（如 MySQL 和 PostgreSQL）的理想选择。这使您可以轻松扩展和管理数据库，而无需担心底层基础设施。

*   *电子商务与零售*：Cloud SQL 可用于存储和管理电子商务及零售网站的客户数据、库存和销售信息。

*   *内容管理系统*：Cloud SQL 可用于存储和管理网站及其他数字媒体（如图片、视频和文章）的内容。

*   *商业智能与分析*：Cloud SQL 可用于存储和管理商业智能与分析应用（如数据仓库和报表）的数据。

*   *移动与网络应用*：Cloud SQL 可用于存储和管理移动及网络应用（如用户资料、偏好设置和交易记录）的数据。

*   *物联网与传感器数据*：Cloud SQL 可用于存储和管理来自物联网设备及传感器（如温度、湿度和其他环境数据）的数据。

*   *微服务*：Cloud SQL 可用于存储和管理基于微服务的应用数据，允许每个微服务拥有自己的数据库。

*   *多云与混合云*：Cloud SQL 可作为多云或混合云策略的一部分，使组织能够在云端存储和管理数据，同时为特定用例维护本地数据库。

这些只是 Cloud SQL 众多可能用例中的几个例子。凭借其可扩展性、高可用性和自动备份功能，Cloud SQL 可用于各种应用和工作负载。

以下是使用 Cloud SQL 的一个真实用例。

假设一家公司想要构建一个电子商务网站，该网站需要一个数据库来存储客户订单、产品详情和客户信息。他们决定使用 Google Cloud SQL 来管理数据库，因为它提供了完全托管的 SQL 数据库服务，能够处理高流量并确保数据库的高可用性。

他们使用 Google Cloud Console 创建一个 Cloud SQL 实例，并将其配置为使用 MySQL。然后，他们创建一个数据库模式，其中包含用于存储客户订单、产品详情和客户信息的表。该 Cloud SQL 实例提供一个公共 IP 地址，可用于从互联网连接到数据库。

接下来，他们使用 Java 或 Python 等编程语言创建一个 Web 应用。该 Web 应用使用 JDBC 驱动程序连接到 Cloud SQL 实例，并运行 SQL 查询来插入、更新和检索数据库中的数据。他们还使用 Cloud SQL 的内置备份功能，以确保数据安全，并在发生任何故障时能够恢复。

随着电子商务网站的发展以及更多用户开始使用，他们决定为 Cloud SQL 实例添加只读副本。只读副本使他们能够扩展读取性能并提高网站的响应速度。他们还可以使用这些副本来运行分析工作负载和生成报告。

此外，他们使用 Cloud SQL 的私有 IP 功能，从其 VPC 网络内部安全且私密地连接到数据库。这确保了数据在 Web 应用和数据库之间传输时受到保护和加密。

在这个案例中，通过使用 Cloud SQL，该公司能够为其电子商务网站构建一个可扩展且高可用的数据库，同时最大限度地减少了管理数据库基础设施所需的工作量。

现在，我们来讨论一下 Cloud SQL 是最佳选择的情况。

### Cloud SQL 是最佳选择的情况

在以下几种情况下，使用 Cloud SQL 是最佳选择：

*   *当您的工作负载可变时*：当您的工作负载可变时，Cloud SQL 是一个不错的选择，因为您只需为使用的资源付费，并且可以根据需要轻松扩展或缩减。

*   *当您需要完全托管的关系型数据库时*：Cloud SQL 是一项完全托管的服务，因此您无需担心管理和维护底层基础设施（如服务器和存储）。这使您可以专注于开发和部署应用。

*   *当您需要高可用性时*：Cloud SQL 会自动跨多个可用区复制数据，以确保即使在某个可用区发生故障时，您的数据也始终可用。

*   *当您需要自动备份时*：Cloud SQL 会自动每天备份您的数据，因此您无需担心手动备份数据库。

*   *当您需要与其他 GCP 服务集成时*：Cloud SQL 可以轻松与其他 GCP 服务（如 Cloud App Engine、Cloud Dataflow 和 BigQuery）集成，从而方便地构建和部署使用 Cloud SQL 中存储数据的应用。

*   *当您需要经济高效的解决方案时*：Cloud SQL 可能比运行自管理数据库更具成本效益，尤其是在您的工作负载可变的情况下。您只需为使用的资源付费，并且可以根据需要轻松扩展或缩减。

*   *当您需要灵活的解决方案时*：Cloud SQL 支持不同类型的 SQL 数据库，使您能够选择最符合需求的数据库。

*   *当您需要满足特定合规性要求时*：Cloud SQL 可用于存储和管理受特定合规性要求（如 HIPAA 和 PCI DSS）约束的数据。

对于希望获得关系型数据库的优势，但又不想承担管理和扩展基础设施的运营开销和成本的组织来说，Cloud SQL 是一个绝佳的选择。当您需要一个完全托管、可扩展、高可用且能与其他 GCP 服务集成，并可用于各种应用和工作负载的关系型数据库解决方案时，它是最佳选择。



## 使用 Java 与 Cloud SQL 交互

Java 是一种流行的编程语言，可用于与 Cloud SQL 交互。要使用 Java 与 Cloud SQL 交互，你需要使用一个支持你所使用的数据库（例如 MySQL 或 PostgreSQL）及其版本的 JDBC 驱动程序。

以下是一个使用 Java 连接到运行 MySQL 的 Cloud SQL 实例的示例：

1.  将 MySQL JDBC 驱动程序添加到你的类路径中。你可以从 MySQL 网站下载该驱动程序。

2.  使用 JDBC 驱动程序创建到 Cloud SQL 实例的连接。可以使用以下代码完成此操作：

    ```
    String url = "jdbc:mysql://" + hostname + ":" + port + "/" + dbName;
    Connection connection = DriverManager.getConnection(url, username, password);
    ```

3.  连接建立后，你可以使用标准的 JDBC 调用来与数据库交互。例如，要执行 SQL 查询，可以使用以下代码：

    ```
    Statement statement = connection.createStatement();
    ResultSet resultSet = statement.executeQuery("SELECT * FROM myTable");
    ```

4.  完成与数据库的交互后，你应该通过调用以下代码来关闭连接：

```
connection.close();
```

还有一些库和框架可用于在 Java 中与 Cloud SQL 交互。例如，Spring 框架提供了一个抽象层，使得与数据库（包括 Cloud SQL）的交互变得简单。另一个例子是 Hibernate，它是一个对象关系映射（ORM）框架，可以以更面向对象的方式与数据库交互，并且可以与 Spring 集成以提高效率。

你可以使用你所使用数据库的 JDBC 驱动程序，结合标准的 JDBC 调用，在 Java 中与 Cloud SQL 交互。此外，你还可以使用诸如 Spring 和 Hibernate 之类的库和框架，使与数据库的交互更加便捷。

### 使用 Java 在 Cloud SQL 中创建表

要在 Cloud SQL 中创建表，你可以使用标准的 SQL 命令。创建表的语法取决于你所使用的数据库管理系统（DBMS）（例如 MySQL 或 PostgreSQL）。以下是一个在运行 MySQL 的 Cloud SQL 实例中创建表的示例：

```
CREATE TABLE myTable (
id INT PRIMARY KEY,
name VARCHAR(255) NOT NULL,
age INT NOT NULL
);
```

此示例创建了一个名为 `myTable` 的表，包含三列：`id`、`name` 和 `age`。`id` 列被设置为主键，且不能包含空值。`name` 和 `age` 列也不能包含空值。

你还可以向表中添加其他约束。例如，你可以像这样为某列添加唯一约束：

```
CREATE TABLE myTable (
id INT PRIMARY KEY,
name VARCHAR(255) NOT NULL UNIQUE,
age INT NOT NULL
);
```

你还可以创建带有外键约束、索引等的表。这些附加功能的语法取决于你所使用的 DBMS。

你也可以使用像 Java 这样的编程语言来创建表。该过程与我之前描述的“使用 Java 与 Cloud SQL 交互”的过程类似：你需要打开一个到 Cloud SQL 实例的连接，创建一个语句，然后执行查询。

```
String createTableSQL = "CREATE TABLE myTable ("
+ "id INT PRIMARY KEY, "
+ "name VARCHAR(255) NOT NULL, "
+ "age INT NOT NULL)";
Statement createTable = connection.createStatement();
// 执行查询，并获取一个布尔值，指示它是否返回了结果集
createTable.execute(createTableSQL);
```

需要注意的是，创建表也可以使用 Cloud SQL 网页控制台、Cloud SQL 命令行工具和 `gcloud` 命令行工具来完成。

简而言之，要在 Cloud SQL 中创建表，你可以使用标准的 SQL 命令或像 Java 这样的编程语言。根据你的使用场景和你所使用的数据库管理系统，你可能会发现其中一种方法比另一种更方便。

### 使用 Java 向 Cloud SQL 表中插入数据

要向 Cloud SQL 中的表插入数据，你可以使用标准的 SQL `INSERT` 命令。以下是一个向运行 MySQL 的 Cloud SQL 实例中名为 `myTable` 的表插入数据的示例：

```
INSERT INTO myTable (id, name, age) VALUES (1, 'John', 25);
```

此示例向 `myTable` 表中插入了一行新数据，其中 `id` 列的值为 1，`name` 列的值为 `John`，`age` 列的值为 25。

你也可以使用以下语法一次插入多行数据：

```
INSERT INTO myTable (id, name, age) VALUES (1, 'John', 25), (2, 'Mike', 30), (3, 'Sara', 35);
```

你还可以使用像 Java 这样的编程语言向表中插入数据。清单 5-1 展示了一个使用 Java 向名为 `myTable` 的表插入数据的示例。

```
PreparedStatement insertData = connection.prepareStatement("INSERT INTO myTable (id, name, age) VALUES (?, ?, ?)");
insertData.setInt(1, 1);
insertData.setString(2, "John");
insertData.setInt(3, 25);
insertData.execute();
清单 5-1
使用 Java 向表中插入数据
```

此示例使用预编译语句向 `myTable` 表插入数据。`setInt()` 和 `setString()` 方法用于设置 `id`、`name` 和 `age` 列的值。`execute()` 方法用于执行预编译语句并将数据插入表中。

你还可以使用 Cloud SQL 网页控制台、Cloud SQL 命令行工具和 `gcloud` 命令行工具向表中插入数据。

简而言之，要向 Cloud SQL 中的表插入数据，你可以使用标准的 SQL `INSERT` 命令或像 Java 这样的编程语言。根据你的使用场景，你可能会发现其中一种方法比另一种更方便。

### 使用 Java 在 Cloud SQL 实例上运行查询

要在 Cloud SQL 实例上运行查询，你可以使用标准的 SQL `SELECT` 命令。以下是一个在运行 MySQL 的 Cloud SQL 实例中运行查询以从名为 `myTable` 的表中选择所有数据的示例：

```
SELECT * FROM myTable;
```

此查询将返回 `myTable` 表中的所有行和列。

你也可以使用带有 `WHERE` 子句的 SQL 语句从表中选择特定数据。示例如下：

```
SELECT name, age FROM myTable WHERE id = 1;
```

此查询将从 `myTable` 表中返回 `id` 等于 1 的行的 `name` 和 `age` 列。

你还可以使用带有 `JOIN` 子句的 SQL 语句从多个表中选择数据。示例如下：

```
SELECT myTable.name, myTable.age, otherTable.address FROM myTable JOIN otherTable ON myTable.id = otherTable.id;
```

此查询将从 `myTable` 和 `otherTable` 表中返回 `id` 列匹配的行的 `name`、`age` 和 `address` 列。

你也可以使用像 Java 这样的编程语言来运行查询。以下是一个使用 Java 运行查询以从名为 `myTable` 的表中选择所有数据的示例：

```
PreparedStatement selectData = connection.prepareStatement("SELECT * FROM myTable");
ResultSet result = selectData.executeQuery();
```

此示例使用预编译语句从 `myTable` 表中选择数据。`executeQuery()` 方法用于执行预编译语句并将结果作为 `ResultSet` 对象返回。然后你可以遍历 `ResultSet` 对象以访问返回的数据。

你还可以使用 Cloud SQL 网页控制台、Cloud SQL 命令行工具和 `gcloud` 命令行工具来运行查询。

简而言之，要在 Cloud SQL 实例上运行查询，你可以使用标准的 SQL `SELECT` 命令或像 Java 这样的编程语言。根据你的使用场景，你可能会发现其中一种方法比另一种更方便。



## 在 Java 应用程序中使用 Cloud SQL 进行数据存储

清单 5-2 展示了一个详细示例，说明如何使用 Google Cloud SQL JDBC 驱动程序连接到运行 MySQL 的 Cloud SQL 实例，并在 Java 应用程序中执行基本的 CRUD 操作。

```
import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.PreparedStatement;
import java.sql.ResultSet;
import java.sql.SQLException;
import java.sql.Statement;
public class CloudSQL {
// 替换为您的 Cloud SQL 实例连接名称
private static final String INSTANCE_CONNECTION_NAME = "my-gcp-project:us-central1:my-cloud-sql-instance";
// 替换为您的 Cloud SQL 实例用户名
private static final String USERNAME = "myusername";
// 替换为您的 Cloud SQL 实例密码
private static final String PASSWORD = "mypassword";
// 替换为您的数据库名称
private static final String DATABASE_NAME = "mydatabase";
// JDBC URL 格式：jdbc:mysql://[HOST]:[PORT]/[DB_NAME]
private static final String JDBC_URL = String.format("jdbc:mysql://google/%s?cloudSqlInstance=%s&"
+ "socketFactory=com.google.cloud.sql.mysql.SocketFactory", DATABASE_NAME, INSTANCE_CONNECTION_NAME);
public static void main(String[] args) throws SQLException {
// 连接到 Cloud SQL 实例
Connection connection = DriverManager.getConnection(JDBC_URL, USERNAME, PASSWORD);
// 创建表
createTable(connection);
// 插入数据
insertData(connection);
// 读取数据
readData(connection);
// 更新数据
updateData(connection);
// 删除数据
deleteData(connection);
// 关闭连接
connection.close();
}
private static void createTable(Connection connection) throws SQLException {
Statement statement = connection.createStatement();
statement.executeUpdate("CREATE TABLE mytable (id INT PRIMARY KEY, name VARCHAR(255))");
}
private static void insertData(Connection connection) throws SQLException {
PreparedStatement statement = connection.prepareStatement("INSERT INTO mytable (id, name) VALUES (?, ?)");
statement.setInt(1, 1);
statement.setString(2, "John Doe");
statement.executeUpdate();
}
private static void readData(Connection connection) throws SQLException {
Statement statement = connection.createStatement();
ResultSet resultSet = statement.executeQuery("SELECT * FROM mytable");
while (resultSet.next()) {
int id = resultSet.getInt("id");
String name = resultSet.getString("name");
System.out.println("ID: " + id + ", Name: " + name);
}
}
private static void updateData(Connection connection) throws SQLException {
PreparedStatement statement = connection.prepareStatement("UPDATE mytable SET name = ? WHERE id = ?");
statement.setString(1,"Jane Doe");
statement.setInt(2, 1);
statement.executeUpdate();
}
private static void deleteData(Connection connection) throws SQLException {
PreparedStatement statement = connection.prepareStatement("DELETE FROM mytable WHERE id = ?");
statement.setInt(1, 1);
statement.executeUpdate();
}
}
}
清单 5-2
使用 Google Cloud SQL JDBC 驱动程序连接到运行 MySQL 的 Cloud SQL 实例
```

此示例假设您有一个运行 MySQL 的 Cloud SQL 实例，并且已创建一个名为 `mydatabase` 的数据库。您需要将 `INSTANCE_CONNECTION_NAME`、`USERNAME`、`PASSWORD` 和 `DATABASE_NAME` 变量替换为您的 Cloud SQL 实例的相应值。

该示例首先使用 JDBC URL、用户名和密码连接到 Cloud SQL 实例，然后创建一个包含两列（`id` 和 `name`）的 `mytable` 表。

接着，它向表中插入一行数据，读取数据，更新数据，最后删除数据。

需要注意的是，要使此示例正常工作，您需要将 Google Cloud SQL JDBC 驱动程序添加到应用程序的类路径中。您可以从以下链接下载该驱动程序：[`https://github.com/GoogleCloudPlatform/cloud-sql-jdbc-driver`](https://github.com/GoogleCloudPlatform/cloud-sql-jdbc-driver)。

此外，您还需要启用 Cloud SQL API，并创建一个具有适当权限的服务账号来访问您的 Cloud SQL 实例。

在此示例中，我将密码存储在一个变量中，并提供了内联数据库连接字符串。在生产代码中，您应遵循将密码和连接字符串存储在机密信息中的最佳实践。

## 总结

在本章中，您学习了如何使用 Google Cloud SQL（一种完全托管的数据库服务）在云中存储和管理数据。我们讨论了使用 Cloud SQL 的优势、支持的数据库类型，以及如何使用 Cloud Console、`gcloud` 命令行工具和 API 创建和管理实例。我们还探讨了如何使用 Java 等编程语言与 Cloud SQL 进行交互，以及如何在 Cloud SQL 实例上创建表、插入数据和运行查询。最后，我们通过一个示例，演示了如何使用 Google Cloud SQL JDBC 驱动程序连接到运行 MySQL 的 Cloud SQL 实例，并在 Java 应用程序中执行基本的 CRUD 操作。

