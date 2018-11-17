---

layout: post

title: "Linked List"

date: 2018-11-15 18:07:05 +0800

categories: jekyll update

---

```c
//
//  list.h
//  listtest
//
//  Created by jones on 2018/11/17.
//  Copyright © 2018 jones. All rights reserved.
//

#ifndef list_h
#define list_h

#include<stdio.h>
#define ELementType int
struct Node;
typedef struct Node* PtrToNode;
typedef PtrToNode List;
typedef PtrToNode Position;
struct Node
{
    ELementType Element;
    Position Next;
};

List MakeEmpty(List L);
int IsEmpty(List L);
int IsLast(Position P, List L);
Position Find(ELementType X, List L);
void Delete(ELementType X, List L);
void DeleteAll(ELementType X, List L);

Position FindPrevious(ELementType X, List L);
void Insert(ELementType X, List L, Position P);
void Delete1(ELementType X, List L);
void DeleteList(List L);
Position Header(List L);
Position First(List L);
Position Advance(Position P);
ELementType Retrieve(Position P);

#endif /* list_h */
```

```c
//
//  list.c
//  listtest
//
//  Created by jones on 2018/11/17.
//  Copyright © 2018 jones. All rights reserved.
//

#include "list.h"
#include<stdio.h>
#include<stdlib.h>

List MakeEmpty(List L)
{
    DeleteList(L);
    return L;
}
//Return True if L is empty
int IsEmpty(List L)
{
    return L->Next == NULL;
}

//Return true if P is the last position in list L
//parameter L is unused in this implementation
int IsLast(Position P, List L)
{
    return P->Next == NULL;
}

//Return Position of X in L; NULL if not find
Position Find(ELementType X, List L)
{
    Position P;
    P = L->Next;
    if(P != NULL && P->Element != X)
        P = P->Next;
    return P;
}

//Delete first occurence of X from list
//Assume use of a header node *
void Delete(ELementType X,List L)
{
    Position P, TmpCell;
    
    P = FindPrevious(X, L);
    if(!IsLast(P, L)) {
        TmpCell = P->Next;
        P->Next = TmpCell->Next;
        free(TmpCell);
    }
}

//Instead of usign FindPrevious, use node* pre, *cur
void Delete1(ELementType X, List L)
{
    Position pre, cur;
    for(pre = L, cur = L->Next; cur; pre = cur, cur = cur->Next) {
        if(cur->Element == X) {
            Position Tmpcell = cur;
            pre->Next = Tmpcell->Next;
            free(Tmpcell);
            break;
        }
    }
}

Position FindPrevious(ELementType X, List L)
{
    Position P;
    P = L;
    while(P->Next != NULL && P->Next->Element != X)
        P=P->Next;
    return P;
}
//Delete all occurence of X from list
void DeleteAll(ELementType X, List L)
{
    Position pre, cur;
    for(pre = L, cur = L->Next; cur; ) {
        Position TmpCell;
        if(cur->Element == X) {
            TmpCell = cur;
            pre->Next = TmpCell->Next;
            cur = TmpCell->Next;
            free(TmpCell);
        }
        else {
            pre = cur;
            cur = cur->Next;
        }
    }
}

//Insert after legal position P
//Header implementation assumed
//Parameter L is unused in this implementation
void Insert(ELementType X, List L, Position P)
{
    Position TmpCell;
    TmpCell = (Position)malloc(sizeof(struct Node));
    if(TmpCell == NULL) {
        printf("Out of space");
        return;
    }
    TmpCell->Element = X;
    TmpCell->Next = P->Next;
    P->Next = TmpCell;
}

void DeleteList(List L)
{
    Position P, tmp;
    
    P = L->Next;
    L->Next = NULL;
    while(P != NULL){
        tmp = P->Next;
        free(P);
        P=tmp;
    }
}

```



[jekyll-docs]: https://jekyllrb.com/docs/home

[jekyll-gh]: https://github.com/jekyll/jekyll

[jekyll-talk]: https://talk.jekyllrb.com/

