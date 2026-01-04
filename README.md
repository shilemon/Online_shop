# Online Shop 🛍️
[![License](https://img.shields.io/badge/License-MIT-green.svg)](LICENSE)
[![GitHub Profile](https://img.shields.io/badge/GitHub-iemafzalhassan-blue?logo=github&style=flat)](https://github.com/iemafzalhassan)
![Forks](https://img.shields.io/github/forks/iemafzalhassan/online_shop)
[![Stars](https://img.shields.io/github/stars/iemafzalhassan/online_shop)](https://github.com/iemafzalhassan/online_shop)
![GitHub last commit](https://img.shields.io/github/last-commit/iemafzalhassan/easyshop?color=red)
<p align="center">


A modern, responsive e-commerce platform built with React, featuring a sophisticated dark/light theme system and advanced product management.




## 🌟 Features

- **Responsive Design**: Fully responsive UI that works seamlessly across all devices
- **Theme System**: Sophisticated dark/light theme with smooth transitions
- **Product Management**: 
  - Advanced filtering and search capabilities
  - Dynamic product loading with pagination
  - Detailed product views with modal support
- **Shopping Cart**: 
  - Real-time cart updates
  - Persistent cart state
  - Quick checkout process
- **Modern UI/UX**:
  - Animated components
  - Intuitive navigation
  - Loading states and error handling

## 🚀 Quick Start

### Prerequisites

- Node.js (v16 or higher)
- npm

### Installation

1. Clone the repository:
```bash
git clone https://github.com/iemafzalhassan/online-shop.git
cd online-shop
```

2. Install dependencies:
```bash
npm install
```

3. Start the development server:
```bash
npm run dev
```

4. Build for production:
```bash
npm run build
```

## 🐳 Docker Support

### Building the Docker Image

```bash
# Build the Docker image
docker build -t online-shop .
```

### Running the Container

```bash
# Run the container
docker run -d -p 3000:3000 --name online-shop online-shop
```

The application will be available at `http://localhost:3000`

### Docker Commands Reference

- **Stop the container:**
  ```bash
  docker stop online-shop
  ```

- **Remove the container:**
  ```bash
  docker rm online-shop
  ```

- **View logs:**
  ```bash
  docker logs online-shop
  ```

## 🛠️ Tech Stack

- **Frontend Framework**: React 18
- **Styling**: 
  - Custom CSS with CSS Variables
- **State Management**: React Context API
- **Icons**: Font Awesome
- **Build Tool**: Vite
- **Package Manager**: npm/yarn

## 📦 Project Structure

```
online-shop/
├── src/
│   ├── components/      # Reusable components
│   ├── context/         # React Context providers
│   ├── data/            # Mock data and constants
│   ├── pages/           # Page components
│   ├── utilities/       # Helper functions
│   └── index.css        # Global styles
├── public/              # Static assets
└── package.json         # Project dependencies
```

## 🤝 Contributing

We welcome contributions! Please see our [Contributing Guide](CONTRIBUTING.md) for details.

1. Fork the repository
2. Create your feature branch (`git checkout -b feature/AmazingFeature`)
3. Commit your changes (`git commit -m 'Add some AmazingFeature'`)
4. Push to the branch (`git push origin feature/AmazingFeature`)
5. Open a Pull Request

## 📝 Development Guidelines

- Follow the existing code style and naming conventions
- Write meaningful commit messages
- Add appropriate comments for complex logic
- Update documentation for significant changes
- Add tests for new features


## Blue-Green Deployment Strategy


  ### Prerequisites to try this:

1. EC2 Instance with Ubuntu OS

2. Docker installed & Configured

3. Kind Installed

4. Kubectl Installed

5. Kind Cluster running(Use `kind-config.yml` file present in this root directory.)

>   [!NOTE]
> 
>   You have to go inside root dir of this repo and create Kind Cluster using command: `kind create cluster --config kind-config.yml --name dep-strg`

---

### Steps to implement Blue Green deployment

- Create a namespace first by using:

    ```bash
    kubectl apply -f blue-green-ns.yml
    ```

- Apply the both deployment manifests (`blue-deployment.yaml` and `green-deployment.yaml`) present in the current directory.

    ```bash
    kubectl apply -f blue-deployment.yaml
    kubectl apply -f green-deployment.yaml
    ```

- Open a new tab of terminal and run the watch command to monitor the deployment

    ```bash
    watch kubectl get pods -n blue-green-ns
    ```

- It will deploy `online shop web page without footer` (Blue environment) and `online shop web page with footer` as a new feature (Green environment), now try to access the blue environment web page on browser.

- Run this command to get all resources created in `blue-green-ns` namespace.

    ```bash
    kubectl get all -n online-shop
    ```

- Forward the `online-shop-blue-deployment-service` svc Port with Nodeport

    ```bash
    kubectl port-forward --address 0.0.0.0 svc/online-shop-blue-deployment-service 32000:3001 -n online-shop &
    ```

- Open the inbound rule for port 30001 in that EC2 Instance and check the application(without footer online shop) at URL:

    ```bash
    http://<Your_Instance_Public_Ip>:32000
    ```

- Without footer online shop app image:

    <img width="1914" height="992" alt="Screenshot 2026-01-04 233100" src="https://github.com/user-attachments/assets/3dc43bf2-a63f-41c5-8510-d4587dcb4507" />


---

- Forward the `green-deployment-service` svc Port with Nodeport

    ```bash
    kubectl port-forward --address 0.0.0.0 svc/online-shop-green-deployment-service 31000:3000 -n online-shop &
    ```

- Open the inbound rule for port 30000 in that EC2 Instance and check the application(With footer online shop) at URL:

    ```bash
    http://<Your_Instance_Public_Ip>:30000
    ```

- With the footer online shop image:
    
    <img width="1919" height="991" alt="Screenshot 2026-01-04 233132" src="https://github.com/user-attachments/assets/188ba502-d8f0-4185-98ab-104d9b9e1393" />



>   [!Note]
>
>   Check the URL and port carefully 

---

- Now, go to the `blue-deployment.yml` manifest file and edit the service's selector field with **`online-shop-green`** selector.

- Previous selector:

    <img width="1919" height="885" alt="image" src="https://github.com/user-attachments/assets/7b3f90f1-783b-40aa-9470-c7c828ef3a30" />



- Current selector:

    <img width="1919" height="854" alt="image" src="https://github.com/user-attachments/assets/e1aced78-68af-4f1d-8626-c4ed0d05cefc" />



- Apply `blue-deployment.yaml`

    ```bash
    kubectl apply -f blue-deployment.yaml
    ```
- Kill all the port-forwarding using the command:
    
    ```bash
    pkill -f "kubectl port-forward"
    ```

- Now again, forward the `online-shop-blue-deployment-service` svc Port with Nodeport

    ```bash
    kubectl port-forward --address 0.0.0.0 svc/online-shop-blue-deployment-service 32000:3001 -n online-shop &
    ```

- Check now, the application has added a new feature as `with footer online shop`, as it previously did not have a footer, but it is now added as a feature, check at URL:

    ```bash
    http://<Your_Instance_Public_Ip>:32000
    ```

- Reload the webpage, you will see `with footer online web page` this time at NodePort: 32000. This means you have successfully switched traffic from a blue environment to a green environment.

    ![image](https://github.com/user-attachments/assets/7c400f73-adbe-4bc6-b54b-a87035611c2c)

---

## Cleanup

- Deleting Kind Cluster:

    ```bash
    kind delete cluster --name dep-strg
    ```

---

> [!Note]
>
> If you cannot access the web app after the update, check your terminal — you probably encountered an error like:
>
>   ```bash
>   error: lost connection to pod
>   ```
>
> Don’t worry! This happens because we’re running the cluster locally (e.g., with **Kind**), and the `kubectl port-forward` session breaks when the underlying pod is replaced during deployment (especially with `Recreate` strategy).
>
> 🔁 Just run the `kubectl port-forward` command again to re-establish the connection and access the app in your browser.
>
> ✅ This issue won't occur when deploying on managed Kubernetes services like **AWS EKS**, **GKE**, or **AKS**, because in those environments you usually expose services using `NodePort`, `LoadBalancer`, or Ingress — not `kubectl port-forward`.



## 🔄 CI/CD Pipeline

- GitHub Actions for automated testing and deployment
- Automated version bumping
- Docker containerization support
- Automated dependency updates

## 📈 Future Roadmap

See our detailed [ROADMAP.md](ROADMAP.md) for planned features and improvements.

Key upcoming features:
- User authentication system
- Product reviews and ratings
- Admin dashboard improvements
- Payment gateway integration
- Performance optimizations

