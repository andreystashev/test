#include <iostream>
#include <cstring>
#include <arpa/inet.h> // Для Unix-подобных систем
#include <sys/socket.h>

int main() {
    // Создаем сокет для отправки
    int sendSocket = socket(AF_INET, SOCK_DGRAM, 0);

    sockaddr_in sendAddr;
    sendAddr.sin_family = AF_INET;
    sendAddr.sin_port = htons(12345); // Порт, на котором отправляем
    sendAddr.sin_addr.s_addr = inet_addr("IP_адрес_назначения"); // IP-адрес назначения

    // Создаем сокет для приема
    int receiveSocket = socket(AF_INET, SOCK_DGRAM, 0);

    sockaddr_in receiveAddr;
    receiveAddr.sin_family = AF_INET;
    receiveAddr.sin_port = htons(54321); // Другой порт для приема
    receiveAddr.sin_addr.s_addr = INADDR_ANY;

    // Привязываем сокет для приема к адресу и порту
    bind(receiveSocket, (struct sockaddr*)&receiveAddr, sizeof(receiveAddr));

    const char* message = "Привет, сервер!";
    sendto(sendSocket, message, strlen(message), 0, (struct sockaddr*)&sendAddr, sizeof(sendAddr));

    char buffer[1024];
    sockaddr_in clientAddr;
    int clientAddrLen = sizeof(clientAddr);
    int bytesRead = recvfrom(receiveSocket, buffer, sizeof(buffer), 0, (struct sockaddr*)&clientAddr, &clientAddrLen);
    buffer[bytesRead] = '\0';

    std::cout << "Получено от сервера: " << buffer << std::endl;

    close(sendSocket);
    close(receiveSocket);

    return 0;
}
