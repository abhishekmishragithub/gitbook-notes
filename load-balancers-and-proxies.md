---
description: Random system design notes
---

# 🏗 Load Balancers and Proxies

## Load Balancer Algorithms

Load balancers use various algorithms to distribute incoming network traffic across a group of backend servers. Here are some commonly used algorithms besides Round Robin:

1. **Round Robin:** Distributes requests sequentially among the servers.
2. **Least Connections:** Directs traffic to the server with the fewest active connections.
3. **Least Response Time:** Choose the server with the lowest average response time and the fewest active connections.
4. **Hash-Based:** Distributes requests based on a key you define, such as the client IP address or the request URL.
5. **IP Hash:** A hash function is used on the IP address of the client to determine which server receives the request.
6. **Random:** Randomly selects a server from the pool.
7. **Weighted Algorithms:** Variants of the above methods, but with servers assigned different weights based on capacity or other criteria.

#### Example in Python: Round Robin Algorithm

A simple example of a Round Robin load balancer can be written in Python. This will cycle through a list of servers to evenly distribute the load.

```python

import random

class RoundRobinLoadBalancer:
    """Implements the round robin load balancing algorithm"""

    def __init__(self, servers):
        self.servers = servers
        self.index = 0

    def get_server(self):
        """Returns the next server in the rotation"""
        server = self.servers[self.index]
        self.index = (self.index + 1) % len(self.servers)
        return server

servers = ["Server1", "Server2", "Server3"]
lb = RoundRobinLoadBalancer(servers)

active_connections = []

for i in range(100):
    if random.randint(0, 1) == 0 or not active_connections:
        # Make a new request
        selected_server = lb.get_server()
        active_connections.append(selected_server)
        print(f"Request {i+1}: New request came in. Servers available: {len(servers)}. Allocated to: {selected_server}")
    else:
        # Release a connection
        selected_server = random.choice(active_connections)
        active_connections.remove(selected_server)
        print(f"Request {i+1}: Connection released. Servers available: {len(servers)}. Released from: {selected_server}")
        
```

This code creates a `RoundRobinLoadBalancer` class that takes a list of servers. The `get_server` method returns the next server in the list and updates the index, wrapping around when it reaches the end.

### The least connection implementation

```python
import random

class LeastConnectionsLoadBalancer:
    def __init__(self, servers):
        self.servers = {server: 0 for server in servers}

    def get_server(self):
        server = min(self.servers, key=self.servers.get)
        self.servers[server] += 1
        print(f"New request came in. Server {server} was selected. Current server loads: {self.servers}")
        return server

    def release_connection(self, server):
        if self.servers[server] > 0:
            self.servers[server] -= 1
            print(f"Connection released from Server {server}. Current server loads: {self.servers}")

servers = ["Server1", "Server2", "Server3"]
lb = LeastConnectionsLoadBalancer(servers)

active_connections = []

for _ in range(15):
    if random.randint(0, 1) == 0 or not active_connections:
        # Make a new request
        selected_server = lb.get_server()
        active_connections.append(selected_server)
        print(f"active connections: {active_connections}")
    else:
        # Release a connection
        selected_server = random.choice(active_connections)
        lb.release_connection(selected_server)
        active_connections.remove(selected_server)

```

### IP Hash

This algorithm uses a hash function on the client's IP address to determine which server to send the request to. It ensures a client will consistently be sent to the same server (as long as the server list remains the same).



```python

import random
import hashlib

class IPHashLoadBalancer:
    def __init__(self, servers):
        self.servers = servers

    def get_server(self, ip_address):
        hash_value = int(hashlib.sha256(ip_address.encode()).hexdigest(), 16)
        return self.servers[hash_value % len(self.servers)]

servers = ["Server1", "Server2", "Server3"]
lb = IPHashLoadBalancer(servers)

active_connections = {}

for i in range(100):
    if random.randint(0, 1) == 0 or not active_connections:
        # Make a new request
        ip = f"192.168.1.{random.randint(1, 100)}"
        selected_server = lb.get_server(ip)
        active_connections[ip] = selected_server
        print(f"Request {i+1}: New request from IP {ip} came in. Servers available: {len(servers)}. Allocated to: {selected_server}")
    else:
        # Release a connection
        ip, selected_server = random.choice(list(active_connections.items()))
        del active_connections[ip]
        print(f"Request {i+1}: Connection from IP {ip} released. Servers available: {len(servers)}. Released from: {selected_server}")

```

**How the servers are getting selected**&#x20;

Let's say we have three servers: `Server1`, `Server2`, and `Server3`. And we have a client with IP address `192.168.1.1`.

First, the IP address is hashed using the SHA-256 algorithm. The `hashlib.sha256(ip_address.encode()).hexdigest()` part of the code does this. The `encode()` method converts the IP address string to bytes, which is required by `hashlib.sha256()`. The `hexdigest()` method then converts the hash object to a hexadecimal string.

For example, the SHA-256 hash of `192.168.1.1` might be something like `6dcd4ce23d88e2ee95838f7b014b6284f0b4c05a2c0b301a8a6057f3f8b3b8c6`.

Next, this hexadecimal string is converted to an integer using the `int()` function with base 16, because hexadecimal is a base-16 number system. The resulting integer is very large, for example: `964912989349019283019283019283019283019283019283019283019283019283`.

This integer is then used to select a server from the list. The `% len(self.servers)` operation calculates the remainder of the integer divided by the number of servers. This ensures that the resulting index is within the range of the server list.

For example, if the integer is `964912989349019283019283019283019283019283019283019283019283019283` and we have 3 servers, the remainder when divided by 3 might be 1.

So, the server at index 1 in the list (`Server2`) is selected for the client with IP address `192.168.1.1`.

This way, the same server will always be selected for the same IP address, as long as the list of servers remains unchanged.

### Weighted Round Robin

In this algorithm, each server is assigned a weight, and servers with higher weights receive more connections.

```python
import random

class WeightedRoundRobinLoadBalancer:
    def __init__(self, servers):
        self.servers = servers
        self.index = 0
        self.current_weight = 0
        self.max_weight = max(server['weight'] for server in servers)

    def get_server(self):
        while True:
            self.index = (self.index + 1) % len(self.servers)
            if self.index == 0:
                self.current_weight = self.current_weight - 1
                if self.current_weight <= 0:
                    self.current_weight = self.max_weight
            if self.servers[self.index]['weight'] >= self.current_weight:
                return self.servers[self.index]['name']

servers = [{"name": "Server1", "weight": 1}, {"name": "Server2", "weight": 2}, {"name": "Server3", "weight": 3}]
lb = WeightedRoundRobinLoadBalancer(servers)

active_connections = {}

for i in range(100):
    if random.randint(0, 1) == 0 or not active_connections:
        # Make a new request
        selected_server = lb.get_server()
        active_connections[i] = selected_server
        print(f"Request {i+1}: New request came in. Servers available: {len(servers)}. Allocated to: {selected_server}")
    else:
        # Release a connection
        request_id, selected_server = random.choice(list(active_connections.items()))
        del active_connections[request_id]
        print(f"Request {i+1}: Connection from request {request_id+1} released. Servers available: {len(servers)}. Released from: {selected_server}")

```



### Definition of Proxy and Reverse Proxy

* **Proxy:** A proxy server is an intermediary between a client and the internet. Requests from clients are forwarded to the proxy, which then communicates with the internet on the client's behalf. Proxies are used for various purposes like anonymity, security, and caching.
* **Reverse Proxy:** A reverse proxy, on the other hand, sits in front of one or more servers and forwards client requests to these servers. It's called "reverse" because while a regular proxy represents the client and hides them from the server, a reverse proxy represents the server and hides it from the client.

#### Why Load Balancers are Called Reverse Proxies

Load balancers are often called reverse proxies because they distribute incoming client requests (typically web traffic) across multiple servers, much like a reverse proxy. They increase the efficiency, security, and reliability of the server infrastructure by managing the distribution of traffic. By doing this, they essentially act as a single point of contact for clients, hiding the details of the servers behind them, aligning with the concept of a reverse proxy.

***

## About Proxies

A proxy, or proxy server, is an intermediary server that sits between a user's device (like a computer or smartphone) and the internet. It serves as a gateway through which internet requests and responses flow. Proxies are used for a variety of purposes, including improving security, managing internet usage, and accessing geographically restricted content.

#### Key Functions of a Proxy:

1. **Anonymity:** Proxies can hide a user's IP address, making their internet activities more anonymous. This is often used for privacy reasons.
2. **Security:** A proxy can add a security layer, acting as a firewall or web filter, protecting against web-based threats.
3. **Content Filtering:** Proxies can be used to block or restrict access to certain websites or content, commonly used in corporate, educational, or parental control scenarios.
4. **Caching:** Proxies can cache frequently requested web content, speeding up access for users by serving this cached content instead of fetching it from the internet each time.
5. **Bypassing Restrictions:** Proxies can circumvent geo-restrictions or filters by making requests appear as if they come from a different location.

#### Types of Proxies:

1. **Forward Proxy:** Placed between the client (user) and the internet, used to forward requests from multiple clients to the internet. It’s the "traditional" proxy setup where the proxy represents the user.
2. **Open Proxy:** Accessible by any internet user, often used to anonymize web browsing.
3. **Reverse Proxy:** As previously explained, it's on the server side, directing client requests to the appropriate backend server.

#### How a Proxy Works:

* When a user requests a web page (or any internet resource), the request goes to the proxy server first.
* The proxy server then makes the request on behalf of the user, receives the response from the web server, and forwards the data (web page, file, etc.) back to the user.

This setup allows the proxy to perform various tasks, such as filtering content, caching data, or hiding the user's IP address for privacy reasons. The key distinction between a proxy and a reverse proxy lies in their placement and purpose: a proxy represents and serves the user/client, while a reverse proxy represents and serves the server/backend.

***

## Proxy VS Reverse Proxy

The primary difference between a proxy (often referred to as a forward proxy) and a reverse proxy lies in their operational positioning and the direction of the traffic flow they manage.

#### Proxy (Forward Proxy)

* **Positioning:** A forward proxy is placed between the client (user) and the internet. It's used by the client.
* **The direction of Traffic:** It forwards outbound requests from the client to the internet. When a client requests a website or service, the forward proxy makes the request on behalf of the client and then returns the result to the client.
* **Purpose:** It's typically used for client anonymity, internet usage control, content filtering, and bypassing content restrictions.
* **Client's Perspective:** The client is aware of and typically configures the proxy. It acts on behalf of multiple clients to the servers on the internet.

#### Reverse Proxy

* **Positioning:** A reverse proxy is placed between the internet and the web servers (backend infrastructure). It's used by the server or network providing the service.
* **Direction of Traffic:** It forwards inbound requests from the internet to the backend servers. When a client requests a service or a webpage, the reverse proxy receives this request and forwards it to the appropriate server.
* **Purpose:** It's used for load balancing, caching, SSL termination, and providing an additional layer of security and anonymity for backend servers.
* **Server's Perspective:** The client is usually unaware of the reverse proxy. The reverse proxy acts as an intermediary for one or more servers to the clients on the internet.

In summary, a forward proxy is client-facing, serving the client's interest, and typically known to the client. A reverse proxy is server-facing, serving the server's interest, and is usually transparent to the client. The forward proxy protects client identities and allows them to bypass server restrictions, while the reverse proxy protects server identities and efficiently manages client requests to these servers.
