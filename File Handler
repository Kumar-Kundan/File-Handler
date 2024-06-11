// a modular program in C that accepts command-line arguments to carry out the following tasks:
// - Creating a file.
// - Reading from a specified file, amount of data to read (in bytes), and from where to read
// - Writing to a specified file, amount of data to write (in bytes), and from where to write
// - Display statistical information of a specified file including owner, permissions, and inode.
// - Create a copy of a given file, and the user will provide the source and destination files. 


#include<fcntl.h>
#include<sys/stat.h>  //for stat system call
#include<stdlib.h>  //for malloc function
#include<string.h>
#include<ctype.h>   //for isdigit function
#include<stdio.h>
#include<unistd.h>  //for truncate system call

//declare write function
int writeFile(char *, int , int);


//creating a file
void createFile(char *filename){
    int fd=open(filename,O_RDONLY);
    char choice;

    //file not exist
    if (fd==-1){
        fd=creat(filename,0666);
    }
    //file already exist
    else{
        //closing file decriptor that is opened by open()system call 
        close(fd);

        printf("%s already exist. Do you want overwrite it(y/n).\n",filename);
        scanf(" %c",&choice);
        
        //to overwrite file
        if(choice=='y'||choice=='Y'){
            fd=creat(filename,0666);
            close(fd);
        }
        //not to overwrite file
        else{
            return;
        }
    }
    //Error handling
    if(fd==-1){
        printf("File has not created. !!Error: some internal issue.\n");
        return;
    }
    //no any error
    else{
        printf("%s has created successfully \n",filename);
        printf("do you want write something on file %s(y/n): \n",filename);
        scanf(" %c",&choice);    //adding space before specifier to ignore endline
 
        //writing on file
        if(choice=='y' || choice=='Y'){
            int amount=0;
            printf("Enter amount to write:\n");
            scanf(" %d",&amount);
            getchar();  //to remove newline in buffer
            int n=writeFile(filename,amount,0);
        }
    }
}
//opening a file
int openFile(char *filename){
    int fd=open(filename,O_RDWR);
    char choice;

    //file not exist
    if (fd==-1){
        printf("%s does not exist. Do you want create it(y/n).\n",filename);
        scanf(" %c",&choice);

        //creating file
        if(choice=='y' || choice=='Y'){
            createFile(filename);
            fd=open(filename,O_RDWR);
        }
        else{
            return -1;
        }
    }
    return fd;
}

//size of the file
int sizeOfFile(char *filename){
    //opening file
    int fd=openFile(filename);
    close(fd);
    //file not created
    if(fd==-1)
       return 0;

    //finding size
    struct stat sfile;
    if(stat(filename, &sfile)==-1){
        printf("Error!! in stat system call\n");
        return 0;
    }
    return sfile.st_size;
}

//reading a file
int readFile(char *filename,int amount, int seek){
    //opening file
    int fd=openFile(filename);

    //file not created
    if(fd==-1)
       return 0;

    int size=sizeOfFile(filename);
    //finding size of file to give seek after EOF error
    if(size<seek){
        printf("Error!!,offset is greater than file size.\n");
        printf("size of file=%d \n",size);
        close(fd);
        return 0;
    }

    //moving pointer to desired location
    if((lseek(fd,seek,SEEK_SET)==-1)){
        printf("Error!!,seeking error.");
        close(fd);
        return 0;
    }
    char *buffer=(char*)malloc((amount+1)*sizeof(char));
    if(buffer==NULL){
        printf("Error!!,buffer memory can not be allocated.\n");
        return 0;
    }
    //initializing buffer 
    for(int i=0;i<amount;++i)
        buffer[i]=' ';
    int n=read(fd,buffer,amount);
    close(fd);

    if(n==-1){
        printf("Error!!,reading error.\n");
        return 0;
    }
    //adding null character at end to terminate string properly
    buffer[amount]='\0';
    printf("%d bytes read from %s file\n",n,filename);
    printf("%s \n",buffer);
    
    //freeing buffer
    free(buffer);
    return n;
}

//writing on a file
int writeFile(char *filename, int amount, int seek){
     //opening file
    int fd=openFile(filename);

    //file not created
    if(fd==-1)
       return 0;

    int size=sizeOfFile(filename);
    //finding size of file to give seek after EOF error
    if(sizeOfFile(filename)<seek){
        printf("Error!!,offset is greater than file size.\n");
        printf("size of file=%d \n",size);
        close(fd);
        return 0;
    }
    //moving pointer to desired location
    if((lseek(fd,seek,SEEK_SET)==-1)){
        printf("Error!!,seeking error.");
        close(fd);
        return 0;
    }
    
    char *buffer=(char*)malloc((amount)*sizeof(char));
    if(buffer==NULL){
        printf("Error!!,buffer memory can not be allocated.\n");
        return 0;
    }
    printf("Enter data:\n");
    fgets(buffer,amount,stdin);
    
    //flush whatever left in console
    if(buffer[amount-1]!='\n'){
        while(getchar()!='\n');
    }
    int len = strlen(buffer);
    int n=write(fd, buffer, len);
    printf("%d bytes are successfully written.\n",n);
    close(fd);

    if(n==-1){
        printf("Error!!,writing error.\n");
        return 0;
    }
    free(buffer);
    return n;
}

//Display statistical information of a specified file including owner, permissions, and inode.
void printFileInfo(char *filename){
    //check given file exist or not
    int fd=openFile(filename);

    //file not created
    if(fd==-1)
       return;
    close(fd);

    struct stat sfile;
    int flag=stat(filename, &sfile);
    //Error handling
    if(flag==-1){
        printf("Error!! \n");
        return;
    }
    
    printf("File owner:\n");
    printf("  user id of owner:%d \n",sfile.st_uid);
    printf("  group id of owner:%d \n",sfile.st_gid);
    
    printf("Permissions: %o\n", sfile.st_mode & 0777);

    printf("inode number:%d \n",sfile.st_ino);
}

//Create a copy of a given file, and the user will provide the source and destination files.
void copyFile(char *filename1,char *filename2){
    
    //opening files
    int fd1=openFile(filename1);
    int fd2=openFile(filename2);

    //file1 not created
    if(fd1==-1 )
        return;

    //file2 not created
    if(fd2==-1){
        close(fd1);
        return;
    }
    //content of file2 is less or not
    if(sizeOfFile(filename1)<sizeOfFile(filename2)){
        if(ftruncate(fd2,sizeOfFile(filename1))==-1){
            close(fd1);
            close(fd2);
            return ;
        }
    }
    const int size=256;
    char buffer[size];
    //reading data from file1
    int n;
    while((n=read(fd1, buffer,size))>0){
        
        if(write(fd2, buffer, n)!=n){
            //writing error
            printf("Error!!, writing can not be done on %s",filename2);
            close(fd1);
            close(fd2);
            return;
        }
    }
    //reading error
    if(n==-1){
        printf("ERROR!!, reading can not be done on %s .",filename1);
    }
    else
        printf("copying successful!!");
    close(fd1);
    close(fd2);
}

//number or not
int numberOrNot(char *str){
        while(*str){
            if(!isdigit(*str))
               return 0;
            ++str;
        }
    return 1;
}

//driver code
int main(int argc,char *argv[]){
    if(argc<2){
        printf("Enter operation:\n");
        printf("   create <filename> \n");
        printf("   read <filename> <amount> <offset>\n");
        printf("   write <filename> <amount> <offset> \n");
        printf("   stats <filename> \n");
        printf("   copy <filename1>  <filename2> \n");
        return 0;
    }
    //for creating a file
    else if(strcmp(strlwr(argv[1]),"create")==0){
        if(argc!=3){
           printf("Error!! number of argument is not correct.");
           return 0;
        }
        createFile(argv[2]);   
    }
    //for reading a file
    else if(strcmp(strlwr(argv[1]),"read")==0){
        if(argc!=5){
            printf("Error!! number of argument is not correct.");
            return 0;
        }
        if(!numberOrNot(argv[3]) || !numberOrNot(argv[4])){
            printf("Error!!,argument type is wrong");
            return 0;
        }
        int amount=atoi(argv[3]);
        int seek=atoi(argv[4]);
        int n=readFile(argv[2],amount,seek);
    }

    //for writing on a file
    else if(strcmp(strlwr(argv[1]),"write")==0){
        if(argc!=5){
            printf("Error!! number of argument is not correct.");
            return 0;
        }
        if(!numberOrNot(argv[3]) || !numberOrNot(argv[4])){
            printf("Error!!,argument type is wrong");
            return 0;
        }
        int amount=atoi(argv[3]);
        int seek=atoi(argv[4]);
        int n=writeFile(argv[2],amount,seek);
    }

    else if(strcmp(strlwr(argv[1]),"stats")==0){
        if(argc!=3){
            printf("Error!!, number of arguments is not correct.");
            return 0;
        }
        printFileInfo(argv[2]);
    }
    else if(strcmp(strlwr(argv[1]),"copy")==0){
        if(argc!=4){
            printf("Error!!, number of arguments is not correct.");
            return 0;
        }
        copyFile(argv[2],argv[3]);
    }
    else{
        printf("more number of arguments or operation name is wrong");
    }
    return 0;
}
