#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <winsock2.h>

#pragma comment(lib, "ws2_32.lib")

int main(int argc, char *argv[])
{
    if (argc != 2)
    {
        printf("Usage: tcp_server <PORT>\n");
        return 1;
    }

    WSADATA wsa;
    WSAStartup(MAKEWORD(2,2), &wsa);

    int server = socket(AF_INET, SOCK_STREAM, IPPROTO_TCP);

    if (server == INVALID_SOCKET)
    {
        printf("Socket creation failed\n");
        return 1;
    }

    struct sockaddr_in addr;

    addr.sin_family = AF_INET;
    addr.sin_addr.s_addr = INADDR_ANY;
    addr.sin_port = htons(atoi(argv[1]));

    if (bind(server, (struct sockaddr *)&addr, sizeof(addr)) == SOCKET_ERROR)
    {
        printf("Bind failed\n");
        return 1;
    }

    if (listen(server, 5) == SOCKET_ERROR)
    {
        printf("Listen failed\n");
        return 1;
    }

    printf("Server dang lang nghe...\n");

    struct sockaddr_in clientAddr;
    int clientAddrLen = sizeof(clientAddr);

    int client = accept(server, (struct sockaddr *)&clientAddr, &clientAddrLen);

    if (client == INVALID_SOCKET)
    {
        printf("Accept failed\n");
        return 1;
    }

    printf("Client da ket noi!\n");

    char buf[256];

    while (1)
    {
        int received = recv(client, buf, sizeof(buf)-1, 0);

        if (received <= 0)
            break;

        buf[received] = '\0';

        printf("Nhan tu client: %s", buf);
    }

    closesocket(client);
    closesocket(server);

    WSACleanup();

    return 0;
}
