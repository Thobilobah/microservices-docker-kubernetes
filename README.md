# Multi-Service App: Docker + Kubernetes
This project demonstrates how to build and deploy a multi-service application using Docker and Kubernetes. 

It includes: React frontend, Express.js, Backend Services(Auth, Products, Orders) An API gateway for routing, Docker for containerization. Kubernetes for orchestration

By following this guide, you will spin up the entire stack, step-by-step, even if you are a beginner

## Project Structure
```bash
ecommerce-microservices/
│
├── api-gateway/
├── auth-service/
├── products-service/
├── orders-service/
├── frontend/
├── kubernetes-manifests/
└── docker-compose.yaml
```
Each folder contains a microservice with its own Dockerfile.

## Step by Step Guide (Copy/Paste Friendly)

### Step 1: Prepare Project Folders
```bash
# Create project directory
mkdir ecommerce-microservices && cd ecommerce-microservices

# Create services
mkdir frontend api-gateway auth-service products-service orders-service

# Create Kubernetes folder
mkdir kubernetes-manifests
```
![make directory](https://github.com/Thobilobah/microservices-docker-kubernetes/blob/main/Screenshots%20from%20my%20environment/Screenshot%202026-05-14%20002033.png)

### Step 2: Setup Backend Services

#### `auth-service`

```bash
cd auth-service
npm init -y
npm install express
```
![auth-service](https://github.com/Thobilobah/microservices-docker-kubernetes/blob/main/Screenshots%20from%20my%20environment/Screenshot%20(1388).png)

Create `index.js`:
```bash
const express = require('express');
const app = express();
app.use(express.json());

app.post('/login', (req, res) => {
  const { username, password } = req.body;
  if (username === 'admin' && password === 'password') {
    return res.json({ success: true, token: 'abc123' });
  }
  return res.status(401).json({ success: false, message: 'Invalid credentials' });
});

app.listen(5001, () => console.log('Auth Service running on port 5001'));
```
![index.js](https://github.com/Thobilobah/microservices-docker-kubernetes/blob/main/Screenshots%20from%20my%20environment/Screenshot%202026-05-14%20004149.png)

Create `Dockerfile`:
```bash
FROM node:18
WORKDIR /app
COPY package*.json ./
RUN npm install
COPY . .
EXPOSE 5001
CMD ["node", "index.js"]
```
![Dockerfile](https://github.com/Thobilobah/microservices-docker-kubernetes/blob/main/Screenshots%20from%20my%20environment/Screenshot%202026-05-14%20004355.png)

#### Go back:
```bash
cd ..
```

### `products-service`
```bash
cd products-service
npm init -y
npm install express
```

![products-service](httphttps://github.com/Thobilobah/microservices-docker-kubernetes/blob/main/Screenshots%20from%20my%20environment/Screenshot%202026-05-14%20004624.png)

#### Create `index.js`:
```bash
const express = require('express');
const app = express();

const products = [
  { id: 1, name: 'Laptop', price: 1500 },
  { id: 2, name: 'Phone', price: 800 }
];

app.get('/products', (req, res) => {
  res.json(products);
});

app.listen(5002, () => console.log('Products Service running on port 5002'));
```

![index.js](https://github.com/Thobilobah/microservices-docker-kubernetes/blob/main/Screenshots%20from%20my%20environment/Screenshot%202026-05-14%20004805.png)

#### Create `Dockerfile`:
```bash
FROM node:18
WORKDIR /app
COPY package*.json ./
RUN npm install
COPY . .
EXPOSE 5002
CMD ["node", "index.js"]
```

![Dockerfile](https://github.com/Thobilobah/microservices-docker-kubernetes/blob/main/Screenshots%20from%20my%20environment/Screenshot%202026-05-14%20004857.png)

#### Go back:
```bash
cd ..
```
#### orders-service
```bash
cd orders-service
npm init -y
npm install express
```

![orders-service](https://github.com/Thobilobah/microservices-docker-kubernetes/blob/main/Screenshots%20from%20my%20environment/Screenshot%202026-05-14%20005046.png)

#### create `index.js`:
```bash
const express = require('express');
const app = express();

const orders = [
  { id: 1, product: 'Laptop', quantity: 2 },
  { id: 2, product: 'Phone', quantity: 1 }
];

app.get('/orders', (req, res) => {
  res.json(orders);
});

app.listen(5003, () => console.log('Orders Service running on port 5003'));
```
![index.js](https://github.com/Thobilobah/microservices-docker-kubernetes/blob/main/Screenshots%20from%20my%20environment/Screenshot%202026-05-14%20005240.png)

#### Create 'Dockerfile`:
```bash
FROM node:18
WORKDIR /app
COPY package*.json ./
RUN npm install
COPY . .
EXPOSE 5003
CMD ["node", "index.js"]
```
![Dockerfile](https://github.com/Thobilobah/microservices-docker-kubernetes/blob/main/Screenshots%20from%20my%20environment/Screenshot%202026-05-14%20005413.png)

#### Go back:
```bash
cd ..
```
---

### Step 3: Setup API Gateway
```bash
cd api-gateway
npm init -y
npm install express http-proxy-middleware
```
![api-gateway](https://github.com/Thobilobah/microservices-docker-kubernetes/blob/main/Screenshots%20from%20my%20environment/Screenshot%202026-05-14%20005509.png)

Create `index.js`
```bash
const express = require('express');
const { createProxyMiddleware } = require('http-proxy-middleware');
const app = express();

app.use('/auth', createProxyMiddleware({ target: 'http://auth-service:5001', changeOrigin: true }));
app.use('/products', createProxyMiddleware({ target: 'http://products-service:5002', changeOrigin: true }));
app.use('/orders', createProxyMiddleware({ target: 'http://orders-service:5003', changeOrigin: true }));

app.listen(5000, () => console.log('API Gateway running on port 5000'));
```
![index.js](https://github.com/Thobilobah/microservices-docker-kubernetes/blob/main/Screenshots%20from%20my%20environment/Screenshot%202026-05-14%20005857.png)

Create `Dockerfile`:
```bash
FROM node:18
WORKDIR /app
COPY package*.json ./
RUN npm install
COPY . .
EXPOSE 5000
CMD ["node", "index.js"]
```
![Dockerfile](https://github.com/Thobilobah/microservices-docker-kubernetes/blob/main/Screenshots%20from%20my%20environment/Screenshot%202026-05-14%20010210.png)

Go back:
```bash
cd ..
```
---

### Step 4: Setup Frontend
```bash
cd frontend
npx create-react-app .
```
![setup frontend](https://github.com/Thobilobah/microservices-docker-kubernetes/blob/main/Screenshots%20from%20my%20environment/Screenshot%202026-05-14%20010539.png)

Update `src/App.js`:
```bash
function App() {
  return (
    <div>
      <h1>Microservices App</h1>
      <p>Auth, Products, Orders, API Gateway running!</p>
    </div>
  );
}
export default App;
```
![App.js](https://github.com/Thobilobah/microservices-docker-kubernetes/blob/main/Screenshots%20from%20my%20environment/Screenshot%202026-05-14%20012020.png)

Create `Dockerfile`:
```bash
FROM node:18
WORKDIR /app
COPY package*.json ./
RUN npm install
COPY . .
RUN npm run build
RUN npm install -g serve
CMD ["serve", "-s", "build"]
EXPOSE 3000
```
![Dockerfile](https://github.com/Thobilobah/microservices-docker-kubernetes/blob/main/Screenshots%20from%20my%20environment/Screenshot%202026-05-14%20012157.png)

Go back:
```bash
cd ..
```
### Step 5: Docker Compose file
Create `docker-compose.yaml`:
```bash
version: "3.8"

services:
  auth-service:
    build: ./auth-service
    ports:
      - "5001:5001"

  products-service:
    build: ./products-service
    ports:
      - "5002:5002"

  orders-service:
    build: ./orders-service
    ports:
      - "5003:5003"

  api-gateway:
    build: ./api-gateway
    ports:
      - "5000:5000"
    depends_on:
      - auth-service
      - products-service
      - orders-service

  frontend:
    build: ./frontend
    ports:
      - "3000:3000"
    depends_on:
      - api-gateway
```
![docker-compose.yaml](https://github.com/Thobilobah/microservices-docker-kubernetes/blob/main/Screenshots%20from%20my%20environment/Screenshot%202026-05-14%20012536.png)

### Step 6: Run Locally
```bash
# Build and run all containers
docker-compose up --build
```
![docker-compose.build1](https://github.com/Thobilobah/microservices-docker-kubernetes/blob/main/Screenshots%20from%20my%20environment/Screenshot%202026-05-18%20222109.png)

![docker-compose.build2](https://github.com/Thobilobah/microservices-docker-kubernetes/blob/main/Screenshots%20from%20my%20environment/Screenshot%202026-05-18%20222124.png)

Test
-Frontend:http://localhost:3000
![frontend-test](https://github.com/Thobilobah/microservices-docker-kubernetes/blob/main/Screenshots%20from%20my%20environment/Screenshot%202026-05-23%20150232.png)

---

### Step 7: Push Docker Images
```bash
# Login to Docker Hub
docker login

# Tag & Push each service
docker tag ecommerce-microservices-auth-service princetee/auth-service:latest
docker push princetee/auth-service:latest

docker tag ecommerce-microservices-products-service princetee/products-service:latest
docker push princetee/products-service:latest

docker tag ecommerce-microservices-orders-service princetee/orders-service:latest
docker push princetee/orders-service:latest

docker tag ecommerce-microservices-api-gateway princetee/api-gateway:latest
docker push princetee/api-gateway:latest

docker tag ecommerce-microservices-frontend princetee/frontend:latest
docker push princetee/frontend:latest
```
![docker push](https://github.com/Thobilobah/microservices-docker-kubernetes/blob/main/Screenshots%20from%20my%20environment/Screenshot%202026-05-25%20223012.png)

### Step 8: Kubernetes Manifests
All Kubernetes YAMLs are in `kubernetes-manifests/`
![kubernetes-manifests](https://github.com/Thobilobah/microservices-docker-kubernetes/blob/main/Screenshots%20from%20my%20environment/Screenshot%202026-05-26%20121102.png)

Example: `auth-deployment.yaml`
```bash
apiVersion: apps/v1
kind: Deployment
metadata:
  name: auth-deployment
spec:
  replicas: 1
  selector:
    matchLabels:
      app: auth-service
  template:
    metadata:
      labels:
        app: auth-service
    spec:
      containers:
      - name: auth-container
        image: obataiwo/auth-service:latest
        ports:
        - containerPort: 5001
---
apiVersion: v1
kind: Service
metadata:
  name: auth-service
spec:
  selector:
    app: auth-service
  ports:
    - port: 5001
      targetPort: 5001
   type: clusterIP   
```
![auth-deployment.yaml](https://github.com/Thobilobah/microservices-docker-kubernetes/blob/main/Screenshots%20from%20my%20environment/Screenshot%202026-05-26%20121458.png)

`products.yaml` file
```bash
apiVersion: apps/v1
kind: Deployment
metadata:
  name: products-deployment
spec:
  replicas: 1
  selector:
    matchLabels:
      app: products-service
  template:
    metadata:
      labels:
        app: products-service
    spec:
      containers:
      - name: products-container
        image: obataiwo/products-service:latest
        ports:
        - containerPort: 5002
---
apiVersion: v1
kind: Service
metadata:
  name: products-service
spec:
  selector:
    app: products-service
  ports:
    - port: 5002
      targetPort: 5002
  type: ClusterIP
```
![products.yaml](https://github.com/Thobilobah/microservices-docker-kubernetes/blob/main/Screenshots%20from%20my%20environment/Screenshot%202026-05-26%20121458.png)

`orders.yaml` file
```bash
apiVersion: apps/v1
kind: Deployment
metadata:
  name: orders-deployment
spec:
  replicas: 1
  selector:
    matchLabels:
      app: orders-service
  template:
    metadata:
      labels:
        app: orders-service
    spec:
      containers:
      - name: orders-container
        image: obataiwo/orders-service:latest
        ports:
        - containerPort: 5003
---
apiVersion: v1
kind: Service
metadata:
  name: orders-service
spec:
  selector:
    app: orders-service
  ports:
    - port: 5003
      targetPort: 5003
  type: ClusterIP
```
![order.yaml](https://github.com/Thobilobah/microservices-docker-kubernetes/blob/main/Screenshots%20from%20my%20environment/Screenshot%202026-05-26%20121606.png)

`api-gateway.yaml` file 
```bash
apiVersion: apps/v1
kind: Deployment
metadata:
  name: api-gateway-deployment
spec:
  replicas: 1
  selector:
    matchLabels:
      app: api-gateway
  template:
    metadata:
      labels:
        app: api-gateway
    spec:
      containers:
      - name: api-gateway-container
        image: obataiwo/api-gateway:latest
        ports:
        - containerPort: 5000
---
apiVersion: v1
kind: Service
metadata:
  name: api-gateway-service
spec:
  selector:
    app: api-gateway
  ports:
    - port: 80
      targetPort: 5000
  type: LoadBalancer
```
[api-gateway.yaml](https://github.com/Thobilobah/microservices-docker-kubernetes/blob/main/Screenshots%20from%20my%20environment/Screenshot%202026-05-26%20130306.png)

`frontend.yaml` file
```bash
apiVersion: apps/v1
kind: Deployment
metadata:
  name: frontend-deployment
spec:
  replicas: 1
  selector:
    matchLabels:
      app: frontend
  template:
    metadata:
      labels:
        app: frontend
    spec:
      containers:
      - name: frontend-container
        image: obataiwo/frontend:latest
        ports:
        - containerPort: 3000
---
apiVersion: v1
kind: Service
metadata:
  name: frontend-service
spec:
  selector:
    app: frontend
  ports:
    - port: 80
      targetPort: 3000
  type: LoadBalancer
```
[frontend.yaml](https://github.com/Thobilobah/microservices-docker-kubernetes/blob/main/Screenshots%20from%20my%20environment/Screenshot%202026-05-26%20130508.png)
---

### Step 9: Deploy to Kubernetes
```bash
kubectl apply -f kubernetes-manifests/
kubectl get pods
kubectl get services
```
[kubernetes pods](https://github.com/Thobilobah/microservices-docker-kubernetes/blob/main/Screenshots%20from%20my%20environment/Screenshot%202026-05-26%20131837.png)

Option A: port forward frontend:
```bash
kubectl port-forward svc/frontend-service 3000:80
```
[port-forwarding](https://github.com/Thobilobah/microservices-docker-kubernetes/blob/main/Screenshots%20from%20my%20environment/Screenshot%202026-05-26%20141941.png)

Visit: http://localhost:3000
![local-host](https://github.com/Thobilobah/microservices-docker-kubernetes/blob/main/Screenshots%20from%20my%20environment/Screenshot%202026-05-26%20133841.png)





