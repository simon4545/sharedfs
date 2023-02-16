---
title: python实现中继服务器测试nat类型
---
使用Python实现中继服务器测试NAT类型的过程是相对复杂的，需要涉及到多个模块和库的使用，以下是一个简单的示例，供您参考：

首先，需要安装一个支持STUN协议的Python库，例如stun或pystun。这些库可以用来执行STUN协议的请求和响应，从而确定客户端的公网IP地址和端口。

然后，需要编写一个简单的中继服务器，用于协调两个客户端之间的通信。以下是一个简单的中继服务器示例：

``` Python
import socket

class RelayServer:
    def __init__(self, host, port):
        self.host = host
        self.port = port
        self.sock = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
        self.clients = []

    def start(self):
        self.sock.bind((self.host, self.port))
        self.sock.listen(2)
        print(f"Relay server started on {self.host}:{self.port}")

        while True:
            client, address = self.sock.accept()
            print(f"New client connected: {address}")
            self.clients.append(client)
            if len(self.clients) == 2:
                self.relay()

    def relay(self):
        print("Starting relay...")
        client1 = self.clients[0]
        client2 = self.clients[1]

        while True:
            data = client1.recv(1024)
            if not data:
                break
            client2.sendall(data)

            data = client2.recv(1024)
            if not data:
                break
            client1.sendall(data)

        client1.close()
        client2.close()
        self.clients = []
        print("Relay finished.")

```


这个中继服务器使用TCP协议监听客户端连接，并在有两个客户端连接后开始协调两个客户端之间的通信。当一个客户端发送数据时，中继服务器会将数据转发给另一个客户端，从而实现点对点通信。

最后，需要编写一个客户端程序，用于测试NAT类型。以下是一个简单的客户端示例：


``` Python
import socket
import stun

def get_public_ip_and_port():
    nat_type, external_ip, external_port = stun.get_ip_info()
    print(f"Public IP address: {external_ip}")
    print(f"Public port number: {external_port}")
    return external_ip, external_port

def test_nat_type(server_address, server_port):
    public_ip, public_port = get_public_ip_and_port()
    client_socket = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
    client_socket.connect((server_address, server_port))
    client_socket.sendall(f"{public_ip}:{public_port}".encode())

    while True:
        data = client_socket.recv(1024)
        if not data:
            break
        print(f"Received data: {data.decode()}")

    client_socket.close()

if __name__ == "__main__":
    server_address = "relay.example.com"
    server_port = 12345
    test_nat_type(server_address, server_port)

```

这个客户端程序会首先使用STUN协议获取客户端的公网IP地址和端口号，然后连接到中继服务器并将自己的公网地址和端口号发送出去