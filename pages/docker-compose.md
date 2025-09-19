- 相关概念
	- **服务**：一个服务service就是一个运行在容器中的应用程序。例如，一个web应用可能包含一个web服务器和一个数据库服务
	- 卷：为了持久化存储容器产生的数据，或者在容器和宿主机之间共享，会使用卷（volume）
	- 网络：Docker Compose会自动创建一个默认的网络，使得所有服务都可以在一个隔离的环境中相互通信。可以自定义网络
- docker-compose.yml基本结构：
	- ```yml
	  # 版本号，推荐使用 "3.8" 或更高版本
	  version: '3.8'
	  
	  # 定义所有的服务
	  services:
	    # 服务1：例如一个 web 服务器
	    webapp:
	      # 构建指令
	      build: .
	      # 端口映射 <主机端口>:<容器端口>
	      ports:
	        - "8000:8000"
	      # 卷挂载
	      volumes:
	        - .:/code
	      # 环境变量
	      environment:
	        - DEBUG=true
	  
	    # 服务2：例如一个数据库
	    database:
	      # 直接使用 Docker Hub 上的镜像
	      image: postgres:13
	      # 环境变量
	      environment:
	        POSTGRES_USER: user
	        POSTGRES_PASSWORD: password
	        POSTGRES_DB: dbname
	      # 卷挂载，用于持久化数据库数据
	      volumes:
	        - db_data:/var/lib/postgresql/data
	  
	  # 统一管理所有卷
	  volumes:
	    db_data:
	  ```
-