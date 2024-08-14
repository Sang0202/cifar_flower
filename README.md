# CIFAR-Flower: Federated Learning Demo with CIFAR-10 Dataset
This repository demonstrates federated learning with flower.ai framework using the CIFAR-10 dataset.

## Running with Docker

### 1. Build Docker Images

- Server: Navigate to the flower_server directory and build the Docker image

```bash
docker build -t flower_server .
``` 

- Client: Navigate to the flower_client directory and build the Docker image

```bash
docker build -t flower_client .
``` 

### 2. Create a Docker Network

Create a Docker network to allow communication between the server and clients.
This is an bridge network that lets containers connected to the same bridge network communicate, while providing isolation from containers that aren't connected to that bridge network. It only apply to containers running on the same Docker daemon host.

```bash
docker network create flower_network
```

### 3. Run Docker Containers:
- Run the Flower server in the created network:
```bash
docker run --rm --network flower_network --name flower_server -p 8080:8080 flower_server
```
- Run the first client:
```bash
docker run --rm --network flower_network --name flower_client_1 flower_client
```

- Run the second client:
```bash
docker run --rm --network flower_network --name flower_client_2 flower_client
```
## Running Directly
### Installation
- Server: Install the Flower package:
```bash
pip install flwr
```
- Client: Install the Flower, Torch and Torchvision packages:
```bash
pip innstall flwr torch torchvision
```
### Implementation
- Server: Run the server script:
```bash
py server.py
```
- Client: Run the client script:
```bash
py client.py
```
## Data
### Input
- CIFAR-10: A popular dataset for image recognition tasks, containing 60000 32x32 colour images in 10 classes.
- Transform: Images are converted to tensors and normalized with a mean and standard deviation of 0.5.
- Model: A simple Convolutional Neural Network (CNN) with 2 convolutional layers and 3 fully connected layers.
### Output
- **Model weights**: The model's state dictionary (parameters/ weights) is saved to a file using **`torch.save`**. These weights can be loaded later for further training or classification tasks.

## Connection/ Communication Between Server And Client
Server and client communicate through a gRPC server:
- **Server Side**:  The server receives serialized model weights/parameters from clients, then aggregates these weights (e.g, using FedAvg strategy by default), and send the updated global weights back to all clients.
- **Client Side**: After training with local data, the client serializes the model's weight/parameters and sends them to the server. Upon receiving the global model weights from the server, the client updates its local model and continues training with local data before sending the updated weights back to the server.

In this demo, the **`NumpyClient`** is used, which handles the serialization and deserialization. For more customization, you can directly use the **`Client`** class from Flower.

## Note: Modify the IP address to test :
### Local machine: 
- Server: 
```python
 fl.server.start_server(
        server_address="0.0.0.0:8080",
        config=fl.server.ServerConfig(num_rounds=3),
    )
```
- Client:
```python
    fl.client.start_numpy_client(server_address="127.0.0.1:8080", client=CifarClient())
```
### Docker:
- Server:
```python
fl.server.start_server(
        server_address="0.0.0.0:8080",
        config=fl.server.ServerConfig(num_rounds=3),
    )
```
- Client: 
```python
    fl.client.start_numpy_client(server_address="flower_server:8080", client=CifarClient())
```
### Internet: 
- Server:
```python
fl.server.start_server(
        server_address="IP_Address:8080",
        config=fl.server.ServerConfig(num_rounds=3),
    )
```
- Client:
```python
fl.client.start_numpy_client(server_address="IP_Address:8080", client=CifarClient())
```
    
