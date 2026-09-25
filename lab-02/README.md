# CST8915 Lab 2: 12-Factor Refactor of Algonquin Pet Store

- **Student Name :** Krami Kamal
- **Student ID :** 041273436
- **Course :** CST8915 Full-stack Cloud-native Development
- **Semester :** Fall 2026

---

## Demo Video

🎥 [Watch Demo Video](https://youtu.be/NOeeejqe778)

The video shows the Store Front, Order Service, Product Service, and RabbitMQ each running on their own Azure VM, the public IPs of all four VMs, the environment variables used for configuration (with secrets hidden), and a full order being placed and queued in RabbitMQ.


## Service Repositories

Each microservice has its own repository, as required by the Codebase factor:

- [order-service-lab2](https://github.com/kramikamal/order-service-lab2.git)
- [product-service-lab2](https://github.com/kramikamal/product-service-lab2.git)
- [store-front-lab2](https://github.com/kramikamal/store-front-lab2.git)

---

## Deployment Summary

| Service | VM | Public IP | Port |
| --- | --- | --- | --- |
| Store Front | ``store-front-vm`` | ``135.225.134.180`` | ``8080`` |
| Order Service | ``order-service-vm`` | ``172.160.250.52`` | ``3000`` |
| Product Service | ``product-service-vm`` | ``132.225.115.255`` | ``3030`` |
| RabbitMQ | ``rabbitmq-vm`` | ``9.160.37.84`` | ``5672 (15672 optional)`` |


## Reflection Questions

### 1. What changes did you make to the order-service and product-service to comply with the Configurations and Backing Services factors?

I removed the hard-coded values from both services and moved them into a `.env` file. In the order-service, the RabbitMQ connection string and the port number are now read from environment variables (`RABBITMQ_CONNECTION_STRING` and `PORT`) using the `dotenv` package. In the product-service, the port number is read the same way using the `dotenv` crate in Rust. For Backing Services, I set up RabbitMQ on its own dedicated VM instead of running it locally with the app, and I created a separate `orderapp` account for the order-service to connect to it remotely over its public IP on port 5672, instead of using the local-only `guest` account.

### 2. Why is it important to use environment variables instead of hard-coding configurations in your application?

Environment variables let the same code run in different environments (local, VM, production) without changing the source code, since only the `.env` file changes. It also keeps secrets, like the RabbitMQ password, out of the codebase and out of version control, so they aren't exposed if the repository is public. It makes it easier to move a service to a different VM or IP address, since only the configuration needs updating, not the code.

### 3. Why is it important to have separate repositories for each microservice? How does this help maintain independence and scalability of each service?

Separate repositories let each service be developed, versioned, and deployed on its own schedule, without one service's changes affecting the others. It also means each service can be scaled, restarted, or redeployed independently, for example, adding more Order Service instances without touching the Product Service or Store Front. This matches the microservices idea of loosely coupled services, where each one can be built with its own technology stack and tested and released on its own.


## Challenges and Learnings (Optional)

- Getting the NSG rules right across four VMs took a few tries. The order-service needed to reach RabbitMQ on port 5672 using the RabbitMQ VM's public IP, not `localhost`, since they are now on different machines.
- I had to remember that Vue.js environment variables are baked into the app at build time, so changing the `.env` file in the Store Front required restarting `npm run serve` to take effect.
- The RabbitMQ `guest` account only works from `localhost`, so I had to create the `orderapp` account for the order-service to connect remotely.


## References

- [RabbitMQ Documentation](https://www.rabbitmq.com/docs)
- [Azure Network Security Groups Overview](https://learn.microsoft.com/en-us/azure/virtual-network/network-security-groups-overview)
- [The Twelve-Factor App](https://12factor.net/)