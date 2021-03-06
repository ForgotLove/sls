# OSS访问日志 {#concept_v4b_w3q_zdb .concept}

日志服务支持采集OSS访问日志，并对采集到的OSS访问日志进行实时查询与分析统计、通过多种可视化方式进行分析结果的直观展示。日志服务对OSS访问日志的专业日志采集分析手段，可以简化您的操作审计和事件回溯，让您的工作更有效率。

## 前提条件 {#section_wp1_h5s_zdb .section}

1.  已开通日志服务。
2.  已开通OSS服务，并成功创建了Bucket。
3.  日志服务Project需要和OSS Bucket在同一个阿里云账号的同一Region下。

## 配置步骤 {#section_kt5_l5s_zdb .section}

## 1 日志采集授权 {#section_kmp_m5s_zdb .section}

单击[快捷授权](https://ram.console.aliyun.com/?spm=a2c4g.11186623.2.5.jntiYI#/role/authorize?request=%7B%22Requests%22%3A%20%7B%22request1%22%3A%20%7B%22RoleName%22%3A%20%22AliyunLogArchiveRole%22%2C%20%22TemplateId%22%3A%20%22Archive%22%7D%7D%2C%20%22ReturnUrl%22%3A%20%22https%3A//sls.console.aliyun.com/%22%2C%20%22Service%22%3A%20%22Log%22%7D)为日志服务授权，授权后日志服务有权限将OSS访问日志分发到您的Logstore中。

若您之前没有授权，也可以在OSS控制台首页单击**日志分析** \> **日志采集授权**，根据提示完成授权。

## 2 关联Bucket {#section_vdd_n5s_zdb .section}

1.  在OSS控制台首页单击**日志分析** \> **管理日志服务**，进入日志分析页面。
2.  单击**新建关联**，进入新建关联步骤。

    ![](images/5431_zh-CN.png "新建关联")

    1.  **选择/新建项目**。

        选择日志服务Project所属的区域、日志服务项目名称，并单击**下一步**。

        **说明：** 

        -   **您的日志服务Project与OSS Bucket须处于同一区域，多个Bucket日志可以采集至同一个Logstore中**。
        -   如您之前没有在当前区域中创建对应的日志服务Project，您需要输入Project名称新建一个。
    2.  **选择日志库**。

        选择对应的Logstore名称，并单击**下一步**。

        **说明：** 如您之前没有在当前区域中创建对应的日志服务Logstore，您需要输入Logstore名称新建一个。

    3.  **关联Bucket**。

        选择对应的Bucket名称，并单击**提交**。您可以选择将当前区域下的多个Bucket与该Logstore关联。

        ![](images/5432_zh-CN.png "关联Bucket")

    您已成功建立分发规则。请根据页面提示，前往日志服务控制台设置索引信息。


## 3 配置查询分析 {#section_t3j_55s_zdb .section}

1.  **关联Bucket**配置完成后，根据页面提示，单击弹出对话框中的**设置索引**，跳转到日志服务控制台查询分析 & 可视化页面。

    ![](images/5433_zh-CN.png "配置查询分析")

2.  日志服务已预设了OSS日志所需的查询索引，字段说明请查看[OSS日志格式](#)。确认后单击**下一步**。

    ![](images/5434_zh-CN.png "配置索引")

    **说明：** 系统默认为您关联了Bucekt的Logstore创建4个特定的仪表盘，配置完成后您可以在仪表盘页面查看。您也可以在OSS控制台的日志分析页面中单击对应日志库**分析日志**，并单击左侧的仪表盘名称，直接查看仪表盘。

    ![](images/5435_zh-CN.png "分析日志")

3.  您可以按需要配置日志投递、ETL，或者直接单击**确认**，完成配置。

## 默认仪表盘 {#section_kd4_x5s_zdb .section}

日志服务提供了4个开箱即用的报表中心：

-   **oss\_operation\_center，展示总体运营状况的信息**

    ![](images/5436_zh-CN.png "oss_operation_center")

-   **oss\_access\_center，展示针对访问日志的统计信息**

    ![](images/5437_zh-CN.png "oss_access_center")

-   **oss\_performance\_center，展示针对性能的统计信息**

    ![](images/5438_zh-CN.png "oss_performance_center")

-   **oss\_audit\_center，展示文件删除修改的统计信息**

    ![](images/5439_zh-CN.png "oss_audit_center")


## OSS日志格式 {#section_h2b_cvs_zdb .section}

|日志类型|说明|
|:---|:-|
|访问日志|记录了对应的Bucket的所有访问日志。实时收集。|
|批量删除日志|记录了批量删除的具体删除信息。实时收集。**说明：** 当用户调用DeleteObjects时，访问日志中会有一条请求记录。但因为删除的文件信息存在用户请求的HTTP Body中，访问日志中的`object`会是`-`，要参考具体的删除文件的列表，需要查看批量删除日志，可以通过`request_id`关联。

|
|每小时计量日志|记录特定Bucket每个小时累计的一些统计计量，延迟为几小时，用于辅助分析。|

## 访问日志 {#section_odd_2vs_zdb .section}

|字段|含义|示例|
|:-|:-|:-|
|\_\_topic\_\_|主题，固定为`oss_access_log`。|-|
|time|访问时间，即OSS收到请求的时间，如果需要时间戳可以使用域`__time__`。|27/Feb/2018:13:58:45|
|owner\_id|Bucket拥有者阿里云ID。|12345678|
|User-Agent|HTTP的User-Agent头。|curl/7.15.5|
|logging\_flag|是否开启logging，即是否开启了日志定期导出到OSS Bucket的功能。|true|
|bucket|Bucket名称。|bucket123|
|content\_length\_in|请求头中Content-Length的值（字节）。|12345|
|content\_length\_out|回应头中Content-Length的值（字节）。|12345|
|object|用户请求的object，URL编码，查询时可以使用`select url_decode(object)`解码。|data%2Fcur\_file.txt|
|object\_size|对象大小，即对应请求对象的大小，单位为Bytes。|1234|
|operation|访问类型，完整访问类型列表与描述，请参考[访问类型](#)。|GetObject|
|bucket\_location|Bucket所在集群，一般格式是`oss-<region>-id`。|oss-cn-beijing-f|
|request\_uri|用户请求的URI，包括query-string，路径是URL编码，查询时可以使用`select url_decode(request_uri)`解码。|/1518085703067732%2Fcur\_file.txt HTTP/1.1|
|error\_code|OSS返回的错误码，完整错误码列表与描述，请参考[OSS错误响应](../../../../intl.zh-CN/常见错误排除/OSS错误响应.md)。|NoSuchKey|
|request\_length|HTTP请求的大小，包括header。单位为字节。|376|
|client\_ip|请求发起的IP地址，即客户端IP、其网络防火墙或者Proxy的IP。|1.2.3.4|
|response\_body\_length|HTTP响应中body的大小，即HTTP response的body的大小，不包括header。|123|
|http\_method|HTTP请求方法。|GET|
|referer|请求的HTTP Referer。|http://www.abc.com|
|requester\_id|请求者阿里云ID，匿名访问为“-”。|12345678|
|request\_id|请求ID，表示本次请求的ID，用于OSS技术人员问题排查。|5A7C39674857FB9FFFFFF|
|response\_time|请求响应时间，单位为毫秒。|123|
|server\_cost\_time|OSS服务器处理时间，即OSS服务器处理本次请求所花的时间，单位为毫秒。|123|
|http\_type|HTTP请求类型，即http或https请求。|http|
|sign\_type|签名类型，完整签名类型列表和描述，请参考[签名类型](#)。|NormalSign|
|http\_status|HTTP状态，即OSS请求返回的HTTP状态。|200|
|sync\_request|同步请求类型，完整同步请求类型列表和描述，请参考[同步请求类型](#)。|cn|
|bucket\_storage\_type|Bucket存储类型，完整Bucket存储类型列表和描述，请参考[Bucket存储类型](#)。|standard|
|host|请求访问域名。|bucket123.oss-cn-beijing.aliyuncs.com|
|vpc\_addr|用户访问oss的域名对应的vip的地址，整形格式，用于OSS技术人员问题排查。|1234567890|
|vpc\_id|用户通过vpc访问时的vpc id，用于OSS技术人员问题排查。|1234|
|delta\_data\_size|object大小的变化量，若没有变化为0；如果不是上传请求，则为“-” 。|280|

## 批量删除日志 {#section_btd_fvs_zdb .section}

当用户调用DeleteObjects时，访问日志中会有一条请求记录。但因为删除的文件信息存在用户请求的HTTP Body中，访问日志中的`object`会是`-`。参考具体的删除文件的列表，需要查看批量删除日志。批量删除日志的字段及说明如下，可以通过`request_id`关联。

|字段|说明|示例|
|:-|:-|:-|
|\_\_topic\_\_|主题，固定为`oss_batch_delete_log`。|-|
|client\_ip|请求发起的IP地址，客户端IP或者其网络防火墙或者Proxy的IP。|1.2.3.4|
|user\_agent|User-Agent，HTTP的User-Agent头。|curl/7.15.5|
|bucket|Bucket名称。|bucket123|
|error\_code|OSS返回的错误码，完整错误码列表与描述，请参考[OSS错误响应](../../../../intl.zh-CN/常见错误排除/OSS错误响应.md)。|NoSuchKey|
|request\_length|request的大小，HTTP请求的大小，包括header。单位为字节。|376|
|response\_body\_length|response的body的大小，HTTP response的body的大小，不包括header。|123|
|object|用户请求的object，URL编码，查询时可以使用`select url_decode(object)`解码。|data%2Fcur\_file.txt|
|object\_size|对象大小，对应请求对象的大小，单位Bytes。|1234|
|operation|访问类型，完整访问类型列表与描述，请参考[访问类型](#) 。|GetObject|
|bucket\_location|Bucket所在集群，一般格式是`oss-<region>-id`。|oss-cn-beijing-f|
|http\_method|HTTP请求方法。|POST|
|referer|请求的HTTP Referer。|http://www.abc.com|
|request\_id|请求ID，表示本次请求的ID，用于OSS技术人员问题排查。|5A7C39674857FB9FFFFFF|
|http\_status|HTTP状态，OSS请求返回的HTTP状态。|200|
|sync\_request|同步请求类型，完整同步请求类型列表和描述，请参考[同步请求类型](#)。|cdn|
|request\_uri|用户请求的URI，包括query-string, 路径是URL编码，查询时可以使用`select url_decode(request_uri)`解码。|/1518085703067732%2Fcur\_file.txt HTTP/1.1|
|host|请求访问域名。|bucket123.oss-cn-beijing.aliyuncs.com|
|logging\_flag|是否开启logging，即是否开启了原来的日志定期导出功能。|true|
|server\_cost\_time|OSS服务器处理时间，单位为毫秒。|123|
|owner\_id|Bucket拥有者阿里云ID。|12345678|
|requester\_id|请求者阿里云ID，匿名访问为“-”。|12345678|
|delta\_data\_size|object大小的变化量，若没有变化为0；如果不是上传请求，则为“-”。|280|

## 每小时计量日志 {#section_cll_cvs_zdb .section}

记录特定Bucket每个小时累计的一些统计计量，几小时延迟，仅仅辅助分析参考用。

|字段|说明|示例|
|:-|:-|:-|
|\_\_topic\_\_|主题，固定为`oss_metering_log`。|-|
|owner\_id|Bucket拥有者阿里云ID。|12345678|
|bucket|Bucket名称。|bucket123|
|cdn\_in|CDN流入量，单位为字节。|123|
|cdn\_out|CDN流出量，单位为字节。|123|
|get\_request|GET请求次数。|123|
|intranet\_in|内网流入量，单位为字节。|123|
|intranet\_out|内网流出量，单位为字节。|123|
|network\_in|外网流入量，单位为字节。|123|
|network\_out|外网流出量，单位为字节。|123|
|put\_request|PUT请求次数。|123|
|storage\_type|Bucket存储类型，完整Bucket存储类型列表和描述，请参考[Bucket存储类型](#)。|standard|
|storage|Bucket存储量，单位为字节。|123|
|metering\_datasize|非标准存储的计量数据大小。|123|
|process\_img\_size|处理的图像大小，单位为字节。|123|
|process\_img|处理图像。|123|
|sync\_in|同步流入量，单位为字节。|123|
|sync\_out|同步流出量，单位为字节。|123|
|start\_time|计量开始时间戳。|1518084000|
|end\_time|计量截止时间戳，一般一小时一个计量时间单位。|1518087600|
|region|Bucket所在区域。|cn-beijing|
|bucket\_location|Bucket所在集群，一般格式是`oss-<region>-id`。|oss-cn-beijing-f|

## 访问类型 {#section_qll_cvs_zdb .section}

|操作值|描述|
|:--|:-|
|AbortMultiPartUpload|断点上传-中止|
|AppendObject|追加上传文件|
|CommitTransition|CommitTransition|
|CompleteUploadPart|完成断点上传|
|CopyObject|复制文件|
|DeleteBucket|删除Bucket|
|DeleteLiveChannel|删除LiveChannel|
|DeleteObject|删除文件|
|DeleteObjects|删除多个文件|
|ExpireObject|ExpireObject|
|GetBucket|列举文件|
|GetBucketAcl|获取Bucket权限|
|GetBucketCors|查看Bucekt的CORS规则|
|GetBucketEventNotification|获取Bucekt通知配置|
|GetBucketInfo|查看Bucket信息|
|GetBucketLifecycle|查看Bucket的Lifecycle配置|
|GetBucketLocation|查看Bucket区域|
|GetBucketLog|查看Bucket访问日志配置|
|GetBucketReferer|查看Bucket防盗链设置|
|GetBucketReplication|查看跨区域复制|
|GetBucketReplicationProgress|查看跨区域复制进度|
|GetBucketStat|获取bucket的相关信息|
|GetBucketWebSite|查看Bucket的静态网站托管状态|
|GetLiveChannelStat|获取LiveChannel状态信息|
|GetObject|读取文件|
|GetObjectAcl|获取文件访问权限|
|GetObjectInfo|获取文件信息|
|GetObjectMeta|查看文件信息|
|GetObjectSymlink|获取symlink文件的详细信息|
|GetPartData|获取断点文件块数据|
|GetPartInfo|获取断点文件块信息|
|GetProcessConfiguration|获取Bucekt图片处理配置|
|GetService|列举Bucket|
|HeadBucket|查看Bucket信息|
|HeadObject|查看文件信息|
|InitiateMultipartUpload|初始化断点上传文件|
|ListMultiPartUploads|列举断点事件|
|ListParts|列举断点块状态|
|Options|Options|
|PostObject|表单上传文件|
|PostProcessTask|提交相关的数据处理，比如截图等|
|PostVodPlaylist|创建LiveChannel点播列表|
|ProcessImage|图片处理|
|PutBucket|创建Bucket|
|PutBucketCors|设置Bucket的CORS规则|
|PutBucketLifecycle|设置Bucket的Lifecycle配置|
|PutBucketLog|设置Bucket访问日志|
|PutBucketWebSite|设置Bucket静态网站托管模式|
|PutLiveChannel|创建LiveChannel|
|PutLiveChannelStatus|设置LiveChannel状态|
|PutObject|上传文件|
|PutObjectAcl|修改文件访问权限|
|PutObjectSymlink|创建symlink文件|
|RedirectBucket|bucket endpoint重定向|
|RestoreObject|解冻文件|
|UploadPart|断点上传文件|
|UploadPartCopy|复制文件块|
|get\_image\_exif|获取图片的exif信息|
|get\_image\_info|获取图片的长宽等信息|
|get\_image\_infoexif|获取图片的长宽以及exif信息|
|get\_style|获取Bucket样式|
|list\_style|列举Bucket的样式|
|put\_style|创建Bucket样式|

关于每个操作的具体信息，请参考[API概览](../../../../intl.zh-CN/API 参考/API概览.md)。

## 同步请求类型 {#section_h4f_nvs_zdb .section}

|同步请求类型|描述|
|:-----|:-|
|-|一般请求|
|cdn|CDN回源|

## 签名类型 {#section_sky_jws_zdb .section}

|签名类型|描述|
|:---|:-|
|NotSign|未签名|
|NormalSign|一般方式签名|
|UriSign|通过URL签名|
|AdminSign|管理员账号|

关于签名的进一步信息，请参考[用户签名验证](../../../../intl.zh-CN/API 参考/访问控制/用户签名验证.md)。

## Bucket存储类型 {#section_vml_cvs_zdb .section}

|存储类型|描述|
|:---|:-|
|standard|标准存储类型|
|archive|归档存储类型|
|infrequent\_access|低频访问存储类型|

关于每个存储类型的进一步信息，请参考[存储类型介绍](../../../../intl.zh-CN/开发指南/存储类型/存储类型介绍.md)。

