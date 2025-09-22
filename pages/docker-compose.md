- 相关概念
	- **服务**：一个服务service就是一个运行在容器中的应用程序。例如，一个web应用可能包含一个web服务器和一个数据库服务
	- **卷**：为了持久化存储容器产生的数据，或者在容器和宿主机之间共享，会使用卷（volume）
	- **网络**：Docker Compose会自动创建一个默认的网络，使得所有服务都可以在一个隔离的环境中相互通信。可以自定义网络
- `docker-compose.yml`基本结构：
	- ```yml
	  name: gis-boot
	  
	  services:
	    db:
	      image: postgis/postgis:16-3.4
	      environment:
	        POSTGRES_DB: ${PG_DB:-gis}
	        POSTGRES_USER: ${PG_USER:-postgres}
	        POSTGRES_PASSWORD: ${PG_PASSWORD:-postgres}
	      volumes:
	        - db_data:/var/lib/postgresql/data
	      healthcheck:
	        test: ["CMD-SHELL", "pg_isready -U ${PG_USER:-postgres} -d ${PG_DB:-gis}"]
	        interval: 10s
	        timeout: 5s
	        retries: 5
	        start_period: 30s
	      ports: ["5432:5432"]
	      networks: [backend]
	  
	    app:
	      # 本地构建镜像；如已有镜像可改为 image: your-registry/gis-boot:latest
	      build:
	        context: .
	        dockerfile: Dockerfile
	      environment:
	        SPRING_DATASOURCE_URL: jdbc:postgresql://db:5432/${PG_DB:-gis}
	        SPRING_DATASOURCE_USERNAME: ${PG_USER:-postgres}
	        SPRING_DATASOURCE_PASSWORD: ${PG_PASSWORD:-postgres}
	      depends_on:
	        db:
	          condition: service_healthy   # 等数据库健康再启动
	          restart: true                # db 重启时，app 也同步重启
	      ports: ["8080:8080"]
	      networks: [backend]
	  
	    proxy:
	      image: nginx:alpine
	      volumes:
	        - ./nginx.conf:/etc/nginx/nginx.conf:ro
	      ports: ["80:80"]
	      depends_on:
	        app:
	          condition: service_started   # app 起来就行（不等健康）
	      networks: [backend]
	  
	  volumes:
	    db_data:
	  
	  networks:
	    backend:
	  ```
	- 顶层结构：
		- **name**：可选，作为项目名
		- **services**：
		- **volumes**：
			- 命名卷：由Docker管理路径。
			- 绑定宿主目录：把本机的目录直接映射进去
		- **networks**：
		- configs：
		- **secrets**：