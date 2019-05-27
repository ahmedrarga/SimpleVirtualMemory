#include <stdio.h>
#include <stdlib.h>
#include <string.h>

typedef struct Memory{
    int size;
    int queue;
    struct Node* arr;
} Memory;
typedef struct Node{
    int key;
    char* data;
    int time;
} Node;

Memory mainMem;
char **secMem;
int useLRU;
int timeCounter = 0;

void initRam(int size){
    mainMem.size = size;
    mainMem.arr = (Node*)malloc(sizeof(Node)*size);
    for(int i = 0; i < size; i++){
        mainMem.arr[i].key = i;
        mainMem.arr[i].data = "";
        if(useLRU == 1){
            mainMem.arr[i].time = timeCounter;
        }
    }
    mainMem.queue = 0;
}
void initSec(int size){
    secMem = (char**)malloc(sizeof(char*) * size);
    if(!secMem){
        printf("Error while allocating memory! \n");
        exit(1);
    }
    for(int i = 0; i< size; i++){
        secMem[i] = (char*)malloc(sizeof(char) * 1 + 1);
        if(!secMem[i]){
            printf("Error while allocating memory! \n");
            exit(1);
        }
        else{
            secMem[i] = "";
        }
    }
}
int getMin(){
    Node minNode = mainMem.arr[0];
    for(int i = 0; i < mainMem.size; i++){
        int time = mainMem.arr[i].time;
        int min = minNode.time;
        if(mainMem.arr[i].time < minNode.time ){

            minNode.key = mainMem.arr[i].key;
            minNode.time = mainMem.arr[i].time;
        }
    }
    return minNode.key;
}
void appendToDisk(int key, char *data){
    char *tmp = (char*)malloc(sizeof(char)*(strlen(secMem[key]) + 1));
    strcpy(tmp, secMem[key]);
    secMem[key] = (char*)malloc(sizeof(char) * (strlen(tmp) + strlen(data) + 1));
    strcpy(secMem[key], tmp);
    strcat(secMem[key], data);
}
 void appendToRam(int key){
    if(useLRU != 1) {
        appendToDisk(mainMem.arr[mainMem.queue].key, mainMem.arr[mainMem.queue].data);
        mainMem.arr[mainMem.queue].key = key;
        mainMem.arr[mainMem.queue].data = (char *) malloc(sizeof(char) * (strlen(secMem[key]) + 1));
        mainMem.arr[mainMem.queue].data = secMem[key];
        mainMem.queue++;
        if (mainMem.queue == mainMem.size) {
            mainMem.queue = 0;
        }
    } else{
        int minKey = getMin();
        for(int i = 0; i < mainMem.size; i++){
            if(mainMem.arr[i].key == minKey){
                appendToDisk(mainMem.arr[i].key, mainMem.arr[i].data);
                mainMem.arr[i].key = key;
                mainMem.arr[i].data = (char *) malloc(sizeof(char) * (strlen(secMem[key]) + 1));
                mainMem.arr[i].data = secMem[key];
                mainMem.arr[i].time = timeCounter++;
                break;
            }
        }
    }
}
void printDisk(FILE *fp, char **A, int size){
    fputs("secondary memory[", fp);
    for(int i = 0; i < size; i++){
        fputs(A[i], fp);
        fputs(", ", fp);
    }
    fputs("]\n", fp);
}
int seperate_tokens(char* buff, char** action, char** s){
    if(buff[0] == 'p'){
        return -1;
    }
    int key, i = 0, size = 0, index = 0;
    char *tmp;
    *s = (char*)malloc(sizeof(char)*2);
    tmp = (char*)malloc(sizeof(char) * 6);
    for(i = 0; buff[i] != ' '; i++){
        if(buff[i] != ' '){
            tmp[i] = buff[i];
        }else{
            break;
        }
    }
    tmp[i+1] = '\0';
    *action = (char*)malloc(sizeof(char) * strlen(tmp) + 1);
    strcpy(*action, tmp);
    if(i < strlen(buff)){
        for(int j = i + 1; buff[j] != '\0'; j++){
            if(buff[j] != ' ')
                size++;
            else
                break;
        }
        tmp = (char*)malloc(sizeof(char) * size + 1);
        for(i = i + 1; buff[i] != '\0'; i++){
            if(buff[i] != ' '){
                tmp[index] = buff[i];
                index++;
            }else{
                break;
            }
        }
        tmp[index + 1] = '\0';
        key = atoi(tmp);
        if(i < strlen((buff))) {
            (*s)[0] = buff[i + 1];
            (*s)[1] = '\0';
        }
    }
    return key;
}
int existInRam(int key){
    for(int i = 0; i < mainMem.size; i++){
        if(mainMem.arr[i].key == key){
            return 1;
        }
    }
    return 0;
}
void add(int key, char* data){
    for(int i = 0; i < mainMem.size; i++) {
        if(mainMem.arr[i].key == key) {
            char *tmp = (char *) malloc(sizeof(char) * (strlen(mainMem.arr[i].data) + 1));
            strcpy(tmp, mainMem.arr[i].data);
            mainMem.arr[i].data = (char *) malloc(sizeof(char) * (strlen(tmp) + strlen(data) + 1));
            strcpy(mainMem.arr[i].data, tmp);
            strcat(mainMem.arr[i].data, data);
            break;
        }
    }
}
void write(int key, char *data){
    if (existInRam(key) != 1) {
        appendToRam(key);
        add(key, data);
    } else {
        add(key, data);
        if(useLRU == 1) {
            for (int i = 0; i < mainMem.size; i++) {
                if (mainMem.arr[i].key == key) {
                    int time = timeCounter;
                    mainMem.arr[i].time = timeCounter++;
                }
            }
        }
    }
}
void read(int key) {
    if (existInRam(key) != 1) {
        appendToRam(key);
        read(key);
    }
    for (int i = 0; i < mainMem.size; i++) {
        if (mainMem.arr[i].key == key) {
            if (useLRU)
                mainMem.arr[i].time = timeCounter++;
            printf("Reads key: %d, data: %s\n", key, mainMem.arr[i].data);
            break;
        }
    }
}
int main(int argc, char *argv[]) {
    if(argc != 6) {
        printf("Enter parameters:\n");
        printf("LRU/FIFO: 1 for LRU and evey other integer for FIFO\n");
        printf("input file name\n");
        printf("output file name\n");
        printf("size of secondary memory\n");
        printf("size of main memory\n");
        printf("For example: ./a.out 1 input.txt output(LRU) 1000 50\n");
        exit(1);
    }
    int n = atoi(argv[5]), m = atoi(argv[4]);
    useLRU = atoi(argv[1]);
    initRam(n);
    initSec(m);
    FILE *input = fopen(argv[2], "r");
    FILE *output = fopen(argv[3], "w");
    if (input == NULL || output == NULL) {
        printf("Error with opening files! \nBe sure that the input file is exist\n");
        exit(1);
    }
    char buff[255];
    char ch = 'e';
    while (ch != EOF) {
        char *action, *s;
        fscanf(input, "%[^\n]", buff);
        int key = seperate_tokens(buff, &action, &s);
        if (key == -1) {
            action = (char *) malloc(sizeof(char) * 6);
            action = "print";
            printDisk(output, secMem, m);
        } else {
            if (strcmp(action, "write") == 0) {
                write(key, s);
            } else if (strcmp(action, "read") == 0) {
                read(key);
            }
        }
        ch = (char) getc(input);
    }
}
