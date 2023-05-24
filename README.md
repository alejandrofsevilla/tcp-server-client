# TCP Server/Client
Asynchronous TCP server and client using Boost.Asio. 

## Requirements
- C++17
- cmake 3.22.0
- boost 1.74.0
- googletest 1.11.0

## Example
### Server
```cpp
#include <TcpServer.hpp>
#include <iostream>
#include <thread>
\\...
struct : TcpServer::Observer {
  void onConnectionAccepted(int id) {
    std::cout << "New client connected with id " << id << std::endl;
  };
  void onReceived(int id, const char* data, size_t size) {
    std::cout << "Data received from client with id " << id << ": ";
    std::cout.write(data, size);
    std::cout << std::endl;
  }
  void onConnectionClosed(int id) {
    std::cout << "Client disconnected with id " << id << std::endl;
  };
} observer;

boost::asio::io_context context;
std::thread thread{[&context]() { context.run(); }};
TcpServer server{context, observer};

constexpr uint16_t port{1234};
const auto protocol{boost::asio::ip::tcp::v4()};
server.listen(protocol, port);

server.startAcceptingConnections();
\\...
```
### Client 
```cpp
#include <TcpClient.hpp>
#include <iostream>
#include <thread>
\\...
struct : TcpClient::Observer {
  void onConnected() { std::cout << "Client was connected" << std::endl; };
  void onReceived(const char* data, size_t size) {
    std::cout << "Data received: ";
    std::cout.write(data, size);
    std::cout << std::endl;
  }
  void onDisconnected() {
    std::cout << "Client was disconnected " << std::endl;
  };
} observer;

boost::asio::io_context context;
std::thread thread{[&context]() { context.run(); }};
TcpClient client{context, observer};

constexpr uint16_t port{1234};
auto address{boost::asio::ip::address::from_string("127.0.0.1")};
client.connect({address, port});
\\...
```
