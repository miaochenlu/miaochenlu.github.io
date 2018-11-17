---

layout: post

title: "Linked List"

date: 2018-11-15 18:07:05 +0800

categories: jekyll update

---

```C
#include<stdio.h>
#include<stdlib.h>
#include"list.h"
struct Node 
{
    ELementType Element;
    Position Next;
};

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
```



[jekyll-docs]: https://jekyllrb.com/docs/home

[jekyll-gh]: https://github.com/jekyll/jekyll

[jekyll-talk]: https://talk.jekyllrb.com/

