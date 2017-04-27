# chatroom
use socket in linux

server.c
/*
*
Time:
programmer:
version:
*
*/


#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <unistd.h>


#include <sys/socket.h>
#include <netinet/in.h>


#define MAXLINE 80 
#define SERV_PORT 8000
#define WELCOME "Welcome to Chat Room"


int main(void)
{
struct sockaddr_in servaddr, cliaddr ;
socklen_t cliaddr_len ;
int listenfd,connfd ;
char buf[MAXLINE] ;
char str[INET_ADDRSTRLEN] ;
int i ,n ;
int npid ;
struct user {
char name[20];
char gender[10];
int age;
int IDnum;
char msg[MAXLINE];

} ;
//user inforamtion
listenfd = socket(AF_INET,SOCK_STREAM,0);
bzero(&servaddr,sizeof(servaddr)) ;
servaddr.sin_family = AF_INET ;
servaddr.sin_addr.s_addr = htonl(INADDR_ANY);
servaddr.sin_port = htons(SERV_PORT) ;

bind(listenfd, (struct sockaddr *)&servaddr,sizeof(servaddr));
listen(listenfd,20) ;
printf("Accepting connections ..\n");
while(1){
cliaddr_len = sizeof(cliaddr) ;
if( (connfd = accept(listenfd,(struct sockaddr *)&cliaddr,&cliaddr_len)) < 0){
printf("accept error !\n");
}
npid = fork();
if(npid < 0){
printf("call to fork error! \n");
}


else if(npid == 0){
close(listenfd);
while(1){
n = read(connfd,buf,MAXLINE);
if(n == 0){
printf("the other side has been closed !");
break ;
}
printf("received from %s at PORT %d\n",inet_ntop(AF_INET,&cliaddr.sin_addr,str,sizeof(str)),ntohs(cliaddr.sin_port));
//send to client message for regsiter information inclde name 
write(connfd,WELCOME,sizeof(WELCOME));

//write(STDOUT_FILENO, buf, sizeof(buf));
printf("%s",buf);
write(connfd,buf,n) ;
}


}
else 
close(connfd) ;


}

}


/*



printf("the read number is %d\n",n);
while(1){
n=read(connfd,buf,MAXLINE);
if(n == 0){
printf("the other side has been closed \n");
break ;
}


for(i = 0; i <n ;i++)
buf[i] = toupper(buf[i]);
write(connfd,buf,n) ;
write(STDOUT_FILENO,buf,n);
}
close(connfd);//处理一次就关闭了连接
*/


//客户端代码
client.c
/* client.c */
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <unistd.h>
#include <sys/socket.h>
#include <netinet/in.h>


#define MAXLINE 80
#define SERV_PORT 8000
int main(int argc, char *argv[])
{
struct sockaddr_in servaddr ;
char buf[MAXLINE] ;
int sockfd,n ;
//char *str ;
/*
if(argc != 2){
fputs("usage;./client message \n",stderr);
exit(1);
}
*/
//str = argv[1] ;
sockfd = socket(AF_INET,SOCK_STREAM,0);
bzero(&servaddr,sizeof(servaddr));
servaddr.sin_family = AF_INET ;
inet_pton(AF_INET,"127.0.0.1", &servaddr.sin_addr);
servaddr.sin_port = htons(SERV_PORT);
if( (connect(sockfd,(struct sockaddr *)&servaddr,sizeof(servaddr)) ) < 0)
printf("Connection abnormals!\n");

while(fgets(buf, MAXLINE, stdin) != NULL){
write(sockfd,buf,strlen(buf));
n = read(sockfd,buf, MAXLINE);
if(n == 0){
printf("the other side have been closed");
}
else{
write(STDOUT_FILENO, buf, n);
}
}
close(sockfd);
//write(sockfd,str,strlen(str));

//n = read(sockfd,buf,MAXLINE);
//printf("Response from server : %s\n",buf);
//write(STDOUT_FILENO, buf, n);
//？？
//close(sockfd);
return 0 ;
}
