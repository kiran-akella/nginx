
# NGINX as Load Balancer and Reverse Proxy

NGINX is a powerful, high-performance web server, reverse proxy, and load balancer. It is widely used to distribute incoming traffic across multiple backend servers, ensuring high availability, fault tolerance, and efficient load distribution. This document covers setting up NGINX as a **Load Balancer** and **Reverse Proxy**.


## NGINX as a Reverse Proxy

A reverse proxy works by accepting client requests and forwarding them to a backend server. NGINX can serve as a reverse proxy to forward requests to multiple backend servers. This setup improves security, load distribution, and caching of content.

### Example Configuration

```nginx

server {
    listen 80;
    server_name example.com;

    location / {
        proxy_pass http://backend_servers;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
}

```

In this example, requests to `example.com` are forwarded to a group of backend servers defined as `backend_servers`.

***

## NGINX as a Load Balancer

NGINX can also distribute incoming traffic across multiple backend servers to ensure better performance and redundancy. It balances the load between servers using various algorithms.

### Types of Load Balancing
1. **Round Robin** - Distributes requests to backend servers in a sequential, circular order.
2. **Least Connections** - Sends traffic to the server with the fewest active connections.
3. **IP Hash** - Routes requests based on the client’s IP address to ensure consistent routing.

## Configuring NGINX for Load Balancing

To configure NGINX as a load balancer, you'll define a set of backend servers under the `upstream` directive.

### Example Load Balancer Configuration

```nginx
http {
    upstream backend_servers {
        server backend1.example.com;
        server backend2.example.com;
        server backend3.example.com;
    }

    server {
        listen 80;
        server_name example.com;

        location / {
            proxy_pass http://backend_servers;
            proxy_set_header Host $host;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
            proxy_set_header X-Forwarded-Proto $scheme;
        }
    }
}
```

In this configuration, NGINX will forward requests to one of the `backend_servers`.

### Round Robin Load Balancing
Round Robin is the default method used by NGINX. Requests are distributed sequentially to each server in the `upstream` group.

```nginx
upstream backend_servers {
    server backend1.example.com;
    server backend2.example.com;
    server backend3.example.com;
}
```

### Least Connections Load Balancing
The `least_conn` directive sends requests to the server with the least active connections.

```nginx
upstream backend_servers {
    least_conn;
    server backend1.example.com;
    server backend2.example.com;
    server backend3.example.com;
}
```

### IP Hash Load Balancing
The `ip_hash` directive ensures that each client is routed to the same backend server based on their IP address.

```nginx
upstream backend_servers {
    ip_hash;
    server backend1.example.com;
    server backend2.example.com;
    server backend3.example.com;
}
```

## Testing the Setup

1. **Start NGINX**: After configuring the NGINX configuration file, restart NGINX.
   ```bash
   sudo systemctl restart nginx
   ```

2. **Check the Load Balancing**: You can monitor the distribution of requests to the backend servers by checking the logs or using monitoring tools like `htop` or `netstat` on the backend servers.

3. **Test Failover**: You can simulate server failures to test if NGINX properly reroutes traffic to healthy backend servers. Disable a backend server and observe if traffic is redirected to other servers.

## Conclusion

Using NGINX as a reverse proxy and load balancer helps improve the scalability, availability, and security of your web applications. By distributing traffic intelligently across multiple servers, NGINX ensures that the load is balanced and the system remains responsive even under heavy traffic.
