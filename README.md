# zigbee
#include<fcntl.h>
#include<string.h> 
#include<stdio.h> 
#include <ctype.h>
#include <signal.h>
#include <sys/wait.h>
#include <sys/mount.h>
#include <sys/stat.h>
#include <sys/poll.h> 
#include <time.h>
#include <errno.h>
#include <stdarg.h>
#include <mtd/mtd-user.h> 
#include <sys/types.h>
#include <sys/socket.h> 
#include <sys/un.h> 
#include <sys/reboot.h>
//#include <cutils/sockets.h>
#include <termios.h>
#include <linux/kd.h> 
//#include <linux/keychord.h>
//#include <sys/system_properties.h>
#define LOG_TAG "bonovo_keypad"
//#include <cutils/log.h> 

#include <stdio.h>
#include <string.h> 
#include <sys/types.h> 
#include <errno.h> 
#include <sys/stat.h>
#include <fcntl.h> 
#include <unistd.h>
#include <termios.h>
#include <stdlib.h>
#include <errno.h>

int set_port_option(int fd, int nSpeed, int nBits, char nEvent, int nStop)
{
  struct termios newio,oldio;
  
  tcflush(fd, TCIOFLUSH);
  
  if( tcgerattr(fd,&oldtio) != 0)
  {
    printf("SetupSerial 1\n");
    return -1;
  }
  
  bzero(&newtio, sizeof(newtio));
  newtio.c_cflag |= CLOCAL | CREAD;
  newtio.c_cflag &= ~CSIZE;
  
  switch( nBits )
  {
  case 7:
    newtio.c_cflag |= CS7;
    break;
  case 8:
    newtio.c_cflag |=CS8;
    break;
  }
  
  switch( nEvent ) {
    case 'O':
    newtio.c_cflag |= PARENB;
    newtio.c_cflag |= PARODD; 
    newtio.c_iflag |= (INPCK | ISTRIP);
    break; 
    case 'E':
    newtio.c_iflag |= (INPCK | ISTRIP);
    newtio.c_cflag |= PARENB; 
    newtio.c_cflag &= ~PARODD; 
    break;
    case 'N':
    newtio.c_cflag &= ~PARENB;
    break; }
    
switch( nSpeed ) {
case 2400:
cfsetispeed(&newtio, B2400); cfsetospeed(&newtio, B2400);
break; case 4800:
cfsetispeed(&newtio, B4800);
cfsetospeed(&newtio, B4800);
break; case 9600:
cfsetispeed(&newtio, B9600);
cfsetospeed(&newtio, B9600); break;
case 57600:
cfsetispeed(&newtio, B57600); cfsetospeed(&newtio, B57600);
break; case 115200:
cfsetispeed(&newtio, B115200); cfsetospeed(&newtio, B115200);
break; default:
cfsetispeed(&newtio, B9600);
cfsetospeed(&newtio, B9600);
break; }

if( nStop == 1 ) {
newtio.c_cflag &= ~CSTOPB;
}
else if ( nStop == 2 )
{
newtio.c_cflag |= CSTOPB;
}

newtio.c_cc[VTIME] = 0;
newtio.c_cc[VMIN] = 0;

tcflush(fd,TCIOFLUSH);

if((tcsetattr(fd,TCSANOW,&newtio))!=0) {
printf("com set error\n");
return -1; }
printf("set done!\n");
return 0;

}


int main(void) {
int fd,writebyte,readbyte=0; 
unsigned char buf[100];
char s;
fd = open("/dev/ttyS0",O_RDWR|O_NOCTTY|O_NONBLOCK);
int i = 0; if(fd<0)
printf("\n open wrong.........\n");
else
printf("success !!!!\n");
set_port_option(fd,57600,8,'N', 1); 
printf("------------------\n");
for(;;){ 
readbyte=read(fd,buf,100); 
if(readbyte)
{
for(i=0;i<readbyte;i++)
printf("%c",buf[i]);
printf("\n");
readbyte=0; 
memset(buf,0,30);
}
sleep(1);
}
return 0; }
