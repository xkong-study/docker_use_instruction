# docker_use_instruction
今天运行公司项目在本地用docker上跑，我运行了半天dockerfile build+run，一直都不行，报错redis，postgreSQL找不到host，我摸索半天发现要跑docker-compose，今天我就把这个血泪教训总结一下。

1. **Dockerfile vs Docker Compose**:
   - **Dockerfile**：用于定义和描述如何构建一个Docker容器镜像。
   - **Docker Compose**：是一个用于定义和运行多容器Docker应用程序的工具。通过一个`docker-compose.yml`文件，你可以定义服务、网络和卷，然后使用单个命令将它们全部启动或停止。
2. **服务依赖**：
   - 当你的应用程序依赖于其他服务（如Redis或PostgreSQL）时，单独使用Dockerfile可能会导致问题。因为Dockerfile只构建一个容器，而你的应用可能需要多个容器之间的交互。
   - 使用Docker Compose可以确保所有相关的服务都被正确地启动并连接。
3. **网络**：
   - Docker容器之间的通信通常通过网络来实现。Docker Compose会为你的应用程序创建一个默认的网络，使得容器之间的通信变得简单。
   - 如果你只是运行一个Dockerfile，你可能需要手动设置网络，确保所有的服务都在同一个网络上。
4. **Host问题**：
   - 当你在应用程序中指定服务的host时（例如Redis或PostgreSQL的host），在使用Docker Compose时，你应该使用服务的名称作为host，而不是`localhost`或具体的IP地址。
5. **总结与建议**：
   - 当你的应用程序有多个服务依赖时，使用Docker Compose会更加方便。
   - 始终检查你的配置文件（如`docker-compose.yml`）以确保所有的服务、网络和卷都被正确定义。
   - 当遇到问题时，查看Docker的日志和文档通常很有帮助。
   - 在开始一个新项目或在新环境中工作时，确保了解项目的Docker设置和需求。
  
### 运行项目使用Docker Compose的流程：

1. **定位到项目目录**： 首先，你需要确保你在项目的根目录下，这通常是包含`Dockerfile`和`docker-compose.yml`文件的地方。

   ```
   bashCopy code
   cd /path/to/your/project
   ```

2. **构建Docker镜像**（如果需要）： 如果你的`docker-compose.yml`文件没有引用外部的Docker镜像，而是使用了本地的Dockerfile，那么你需要先构建这些镜像。

   ```
   bashCopy code
   docker-compose build
   ```

3. **启动Docker Compose服务**： 使用以下命令启动在`docker-compose.yml`文件中定义的所有服务。

   ```
   bashCopy code
   docker-compose up -d
   ```

   这里，`-d`标志意味着在后台运行服务。如果你想在前台看到输出，可以省略它。

4. **检查服务状态**： 为了确保所有服务都已成功启动，你可以使用以下命令查看它们的状态。

   ```
   bashCopy code
   docker-compose ps
   ```

5. **查看服务日志**（如果需要）： 如果你遇到问题或只是想查看服务的输出，可以使用以下命令查看特定服务的日志。

   ```
   bashCopy code
   docker-compose logs service-name
   ```

   这里的`service-name`是你在`docker-compose.yml`文件中定义的服务名称。

6. **停止Docker Compose服务**： 当你完成工作并想要停止所有服务时，可以使用以下命令。

   ```
   bashCopy code
   docker-compose down
   ```

注：
对于使用`docker-compose`的项目，通常不需要单独运行`Dockerfile`。`docker-compose.yml`文件会指定如何构建和运行容器，包括使用哪个`Dockerfile`、映射哪些端口、设置哪些环境变量等。

关于端口和如何访问web服务，你需要查看`docker-compose.yml`文件中的`ports`部分。这部分会指定容器内的端口如何映射到宿主机的端口。
