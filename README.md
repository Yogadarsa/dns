#include <netinet/in.h>
#include <string.h>
#include <sys/socket.h>
#include <sys/types.h>
#include <unistd.h>

#define SA struct sockaddr
#define MAX 1
#define PORT 1111

int main()
{
    char domains[3][20]= {"www.google.com", "www.facebook.com", "www.youtube.com"};
    char ips[3][20] = {"142.250.77.100", "157.240.192.35", "142.250.182.46"};

    char domain[20], ip[20];

    int servSockD = socket(AF_INET, SOCK_STREAM, 0);
    struct sockaddr_in servAddr;
    servAddr.sin_family = AF_INET;
    servAddr.sin_port = htons(PORT);
    servAddr.sin_addr.s_addr = INADDR_ANY;

    bind(servSockD, (struct sockaddr*) &servAddr, sizeof(servAddr));

    listen(servSockD, MAX);

    int clientSocket = accept(servSockD, NULL, NULL);

    recv(clientSocket, domain, sizeof(domain), 0);

    int x = 0;

    for(int i = 0; i < 3; i++)
    {
        if(!strcmp(domain, domains[i]))
        {
            strcpy(ip, ips[i]);
            x = 1;
            break;
        }
    }
    
    if(x == 0)
    {
        char error[100] = "Invalid domain name";
        send(clientSocket, error, strlen(error), 0);
    }
    else
        send(clientSocket, ip, strlen(ip), 0);
    
    close(clientSocket);
    shutdown(servSockD, SHUT_RDWR);

    return 0;

}




int main()
{
    char input[20], ip[20];

    int servSockD = socket(AF_INET, SOCK_STREAM, 0);
    struct sockaddr_in servAddr;
    servAddr.sin_family = AF_INET;
    servAddr.sin_port = htons(PORT);
    servAddr.sin_addr.s_addr = INADDR_ANY;

    int connectionStatus = connect(servSockD, (struct sockaddr*) &servAddr, sizeof(servAddr));

    if(connectionStatus < 0)
    {
        printf("Error while connecting to server.\n");
    }
    else
    {
        printf("Enter the domain name: ");
        scanf("%s", input);

        send(servSockD, input, strlen(input), 0);

        recv(servSockD, ip, sizeof(ip), 0);

        printf("Ip address is: %s.\n", ip);

        close(servSockD);
    }

    return 0;

}
