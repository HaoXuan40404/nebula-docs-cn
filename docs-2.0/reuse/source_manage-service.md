
{{ comm.comm_begin }}
{{nebula.name}}支持通过脚本管理服务。
{{ comm.comm_end }}

{{ ent.ent_begin }}
{{nebula.name}}支持使用脚本管理服务和使用 systemd 管理服务。

!!! caution

    这两种方式互不兼容，选择使用其中一种。

{{ ent.ent_end }}

## 使用脚本管理服务

使用脚本`nebula.service`管理服务，包括启动、停止、重启、中止和查看。

!!! note
  
    `nebula.service`的默认路径是`/usr/local/nebula/scripts`，如果修改过安装路径，请使用实际路径。

### 语法

```bash
$ sudo /usr/local/nebula/scripts/nebula.service
[-v] [-c <config_file_path>]
<start | stop | restart | kill | status>
<metad | graphd | storaged | all>
```

|参数|说明|
|:---|:---|
|`-v`|显示详细调试信息。|
|`-c`|指定配置文件路径，默认路径为`/usr/local/nebula/etc/`。|
|`start`|启动服务。|
|`stop`|停止服务。|
|`restart`|重启服务。|
|`kill`|中止服务。|
|`status`|查看服务状态。|
|`metad`|管理 Meta 服务。|
|`graphd`|管理 Graph 服务。|
|`storaged`|管理 Storage 服务。|
|`all`|管理所有服务。|

{{ ent.ent_begin }}
## 使用 systemd 管理服务

为方便使用，{{nebula.name}}企业版支持用 systemd 管理服务，通过`systemctl`启动、停止、重启和查看服务。

!!! note

    - 安装{{nebula.name}}企业版后，systemd 所需的`.service`文件在安装目录的`etc/unit`目录内，使用 RPM/DEB 包安装的 NebulaGraph，会自动将这些`.service`文件放入`/usr/lib/systemd/system`目录内，并且`ExecStart`也会根据指定的{{nebula.name}}安装路径进行生成，因此可以直接使用`systemctl`命令。

    - 对于使用{{dashboard_ent.name}}安装的{{nebula.name}}，不支持使用`systemctl`管理服务。
    
    - 对于其他方式安装的企业版 NebulaGraph，需要用户手动将`.service`文件移动到`/usr/lib/systemd/system`目录内，并修改`.service`文件内的`ExecStart`的文件路径，才可以正常使用`systemctl`命令。
    
### 语法

```bash
$ systemctl <start | stop | restart | status > <nebula | nebula-metad | nebula-graphd | nebula-storaged>
```

|参数|说明|
|:---|:---|
|`start`|启动服务。|
|`stop`|停止服务。|
|`restart`|重启服务。|
|`status`|查看服务状态。|
|`nebula`|管理所有服务。|
|`nebula-metad`|管理 Meta 服务。|
|`nebula-graphd`|管理 Graph 服务。|
|`nebula-storaged`|管理 Storage 服务。|

{{ ent.ent_end }}

## 启动{{nebula.name}}服务

执行如下命令启动服务：

```bash
$ sudo /usr/local/nebula/scripts/nebula.service start all
[INFO] Starting nebula-metad...
[INFO] Done
[INFO] Starting nebula-graphd...
[INFO] Done
[INFO] Starting nebula-storaged...
[INFO] Done
```

{{ ent.ent_begin }}
或者：

```bash
$ systemctl start nebula
```

如果需要设置开机自动启动，命令如下：

```bash
$ systemctl enable nebula
```
{{ ent.ent_end }}


## 停止{{nebula.name}}服务

!!! danger

    请勿使用`kill -9` 命令强制终止进程，否则可能较小概率出现数据丢失。
  
执行如下命令停止{{nebula.name}}服务：

```bash
$ sudo /usr/local/nebula/scripts/nebula.service stop all
[INFO] Stopping nebula-metad...
[INFO] Done
[INFO] Stopping nebula-graphd...
[INFO] Done
[INFO] Stopping nebula-storaged...
[INFO] Done
```

{{ ent.ent_begin }}
或者：

```bash
$ systemctl stop nebula
```
{{ ent.ent_end }}

## 查看{{nebula.name}}服务

执行如下命令查看{{nebula.name}}服务状态：

```bash
$ sudo /usr/local/nebula/scripts/nebula.service status all
```

- 如果返回如下结果，表示{{nebula.name}}服务正常运行。

    ```bash
    [INFO] nebula-metad(33fd35e): Running as 29020, Listening on 9559
    [INFO] nebula-graphd(33fd35e): Running as 29095, Listening on 9669
    [WARN] nebula-storaged after v3.0.0 will not start service until it is added to cluster.
    [WARN] See Manage Storage hosts:ADD HOSTS in https://docs.nebula-graph.io/
    [INFO] nebula-storaged(33fd35e): Running as 29147, Listening on 9779
    ```

  !!! note

        正常启动{{nebula.name}}后，`nebula-storaged`进程的端口显示红色。这是因为`nebula-storaged`在启动流程中会等待`nebula-metad`添加当前 Storage 服务，当前 Storage 服务收到 Ready 信号后才会正式启动服务。从 3.0.0 版本开始，在配置文件中添加的 Storage 节点无法直接读写，配置文件的作用仅仅是将 Storage 节点注册至 Meta 服务中。必须使用`ADD HOSTS`命令后，才能正常读写 Storage 节点。更多信息，参见[管理 Storage 主机](https://docs.nebula-graph.com.cn/{{nebula.release}}/4.deployment-and-installation/manage-storage-host/)。


- 如果返回类似如下结果，表示{{nebula.name}}服务异常，可以根据异常服务信息进一步排查，或者在 [{{nebula.name}}社区](https://discuss.nebula-graph.com.cn/)寻求帮助。

    ```bash
    [INFO] nebula-metad: Running as 25600, Listening on 9559
    [INFO] nebula-graphd: Exited
    [INFO] nebula-storaged: Running as 25646, Listening on 9779
    ```

{{ ent.ent_begin }}
也可以使用`systemctl`命令查看{{nebula.name}}服务状态：

```bash
$ systemctl status nebula
● nebula.service
   Loaded: loaded (/usr/lib/systemd/system/nebula.service; disabled; vendor preset: disabled)
   Active: active (exited) since 一 2022-03-28 04:13:24 UTC; 1h 47min ago
  Process: 21772 ExecStart=/usr/local/ent-nightly/scripts/nebula.service start all (code=exited, status=0/SUCCESS)
 Main PID: 21772 (code=exited, status=0/SUCCESS)
    Tasks: 325
   Memory: 424.5M
   CGroup: /system.slice/nebula.service
           ├─21789 /usr/local/ent-nightly/bin/nebula-metad --flagfile /usr/local/ent-nightly/etc/nebula-metad.conf
           ├─21827 /usr/local/ent-nightly/bin/nebula-graphd --flagfile /usr/local/ent-nightly/etc/nebula-graphd.conf
           └─21900 /usr/local/ent-nightly/bin/nebula-storaged --flagfile /usr/local/ent-nightly/etc/nebula-storaged.conf

3月 28 04:13:24 xxxxxx systemd[1]: Started nebula.service.
...
```

{{ ent.ent_end }}

{{nebula.name}}服务由 Meta 服务、Graph 服务和 Storage 服务共同提供，这三种服务的配置文件都保存在安装目录的`etc`目录内，默认路径为`/usr/local/nebula/etc/`，用户可以检查相应的配置文件排查问题。

## 下一步

- [连接{{nebula.name}}](https://docs.nebula-graph.com.cn/{{nebula.release}}/2.quick-start/3.quick-start-on-premise/3.connect-to-nebula-graph/)<!--这里用外链。-->
