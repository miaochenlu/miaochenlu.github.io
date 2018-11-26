---

layout: post

title: "Priority Queue"

date: 2018-11-26 12:21:05 +0800

categories: jekyll update

---
```c
//
//  main.c
//  Heap
//
//  Created by jones on 2018/11/26.
//  Copyright © 2018 jones. All rights reserved.
//

#include <stdio.h>
#include <stdlib.h>
#define ElementType int
#define MinData -1000

typedef struct HeapStruct *PriorityQueue;
struct HeapStruct
{
    ElementType* Elements;
    int Capacity;
    int Size;
};

PriorityQueue Initialize(int MaxElements)
{
    PriorityQueue H;
    //为Hw分配存储空间  此时Elements只有指针空间
    H = (PriorityQueue)malloc(sizeof(struct HeapStruct));
    //为Elements分配空间
    H->Elements = (ElementType*)malloc((MaxElements+1)*sizeof(ElementType));
    //H初始化
    H->Capacity = MaxElements;
    H->Size = 0;
    H->Elements[0] = MinData;
    return H;
}

void PercolateUp(int p, PriorityQueue H)
{
    int element = H->Elements[p];
    int i;
    for(i = p; H->Elements[i/2] > element; i /= 2)
        H->Elements[i] = H->Elements[i / 2];
    
    H->Elements[i] = element;
}

void PercolateDown(int p, PriorityQueue H)
{
    int child;
    int i;
    ElementType element = H->Elements[p];
    for(i = p; i * 2 <= H->Size; i = child) {
        //choose a child that has smaller key
        child = i * 2;
        if(child != H->Size && H->Elements[child+1] < H->Elements[child])
            child++;
        //percolata down
        if(element > H->Elements[child])
            H->Elements[i] = H->Elements[child];
        else break;
    }
    H->Elements[i] = element;
}

void Insert(ElementType X, PriorityQueue H)
{
    int i;
    //插入元素X,size先加一，然后上滤，如果父亲比较大，则把父亲移下来
    //如果比X小，就跳出循环
    for(i = ++H->Size; H->Elements[i/2] > X; i /= 2)
        H->Elements[i] = H->Elements[i/2];
    
    H->Elements[i] = X;
}

ElementType DeleteMin(PriorityQueue H)
{
    int i, child;
    ElementType MinElement = H->Elements[1];
    ElementType lastElement = H->Elements[H->Size--];
    
    for(i = 1; i * 2 <= H->Size; i = child) {
        //找出儿子中较小的那一个进行下滤
        child = i * 2;
        if(child != H->Size && H->Elements[child+1] < H->Elements[child])
            child++;
        //H->Elements[i]的值是lastElement,如果lastElement大于它孩子的值，则进行下滤，否则跳出循环
        if(lastElement > H->Elements[child])
            H->Elements[i] = H->Elements[child];
        else break;
    }
    
    H->Elements[i] = lastElement;
    return MinElement;
}

void PrintHeap(PriorityQueue H)
{
    int i;
    for(i = 1; i <= H->Size; i++)
        printf("%d ",H->Elements[i]);
}

void BuildHeap(PriorityQueue H)
{
    int N;
    int i;
    scanf("%d", &N);
    H->Size = N;
    for(i = 1; i <= N; i++)
        scanf("%d", &H->Elements[i]);
    
    for(i = N/2; i > 0; i--)
        PercolateDown(i, H);
}

void Free(PriorityQueue H)
{
    free(H->Elements);
    free(H);
}

int main()
{
    PriorityQueue H = Initialize(30);
    BuildHeap(H);
    PrintHeap(H);
    printf("\n");
    Insert(3, H);
    PrintHeap(H);
    printf("\n");
    DeleteMin(H);
    PrintHeap(H);
    printf("\n");
    Free(H);
}

```

[jekyll-docs]: https://jekyllrb.com/docs/home

[jekyll-gh]: https://github.com/jekyll/jekyll

[jekyll-talk]: https://talk.jekyllrb.com/
