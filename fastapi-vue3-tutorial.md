# FastAPI + Vue 3 网站开发教程

## 1. 环境准备

### 后端 (FastAPI)

   ```
   pip install fastapi uvicorn
   ```

### 前端 (Vue 3)

1. 安装 Node.js
2. 安装 Vue CLI:
   ```
   npm install -g @vue/cli
   ```

## 2. 创建后端项目结构

创建一个新的项目文件夹:

```
mkdir fastapi-vue-project
cd fastapi-vue-project
```

## 3. 设置后端 (FastAPI)

1. 在项目根目录创建一个 `backend` 文件夹:

   ```
   mkdir backend
   cd backend
   ```

2. 创建一个 `main.py` 文件:

   ```python
   from fastapi import FastAPI
   from fastapi.middleware.cors import CORSMiddleware

   app = FastAPI()

   # 配置CORS
   app.add_middleware(
       CORSMiddleware,
       # allow_origins=["http://localhost:8080"],
       allow_origins=["*"],
       allow_credentials=True,
       allow_methods=["*"],
       allow_headers=["*"],
   )

   @app.get("/api/hello")
   def read_root():
       return {"message": "Hello from FastAPI!"}

   if __name__ == "__main__":
       import uvicorn
       uvicorn.run(app, host="0.0.0.0", port=8000)
   ```

3. 运行后端服务器:

   ```
   uvicorn main:app --reload
   ```

   现在，您的API应该在 `http://localhost:8000` 运行。

## 4. 设置前端 (Vue 3)

1. 返回到项目根目录,创建Vue项目:

   ```
   vue create frontend
   ```

   选择 Vue 3 和您需要的其他选项。

2. 进入前端目录:

   ```
   cd frontend
   ```

3. 安装 Axios 用于API请求:

   ```
   npm install axios
   ```

4. 修改 `src/App.vue`:

   ```vue
   <template>
     <div id="app">
       <h1>{{ message }}</h1>
     </div>
   </template>

   <script>
   import axios from 'axios'

   export default {
     name: 'App',
     data() {
       return {
         message: ''
       }
     },
     mounted() {
       axios.get('http://localhost:8000/api/hello')
         .then(response => {
           this.message = response.data.message
         })
     }
   }
   </script>
   ```

5. 运行Vue开发服务器:

   ```
   npm run serve
   ```

   现在，您的前端应用应该在 `http://localhost:8080` 运行。

## 5. 连接前后端

确保后端和前端都在运行。访问 `http://localhost:8080`，您应该能看到来自FastAPI的消息。

## 6. 进一步开发

- 在FastAPI中添加更多路由和数据模型
- 在Vue中创建更多组件和页面
- 实现用户认证
- 添加数据库（如SQLAlchemy）
- 部署应用（考虑使用Docker）

恭喜！您现在有了一个基本的FastAPI + Vue 3网站框架。继续构建和扩展您的应用程序！

