## Definition
```c
ADT List {
  数据对象：D = { ai | ai∈ElemSet, i=1,2,...,n, n≧0 }
  数据关系：R = {<ai-1, ai> | ai-1, ai∈D, i=2,3,...,n }
  基本操作：
    InitList(&L);
      操作结果：构造一个空的线性表L
    DestroyList(&L);
      初始条件：线性表 L存在
      操作结果：销毁线性表 L
    ClearList(&L);
      初始条件：线性表 L 存在
      操作结果：将 L 置为空表
    ListEmpty(L);
      初始条件：线性表 L 存在
      操作结果：若 L 为空表，返回 TRUE，否则返回 FALSE
    ListLength(L);
      初始条件：线性表 L 存在
      操作结果：返回 L 中的数据元素个数
    GetElem(L, i, &e);
      初始条件：线性表 L 存在，且 1 <= i <= ListLength(L)
      操作结果：用 e 返回 L 中第 i 个元素的值
    LocateElem(L, e);
      初始条件：线性表 L 存在
      操作结果：返回 L 中第一个值与元素 e 相同的元素在 L 中的位置。若元素不存在，则返回 0
    PriorElem(L, cur_e, &pre_e);
      初始条件：线性表 L 存在
      操作结果：若 cur_e 是 L 中的元素，且不是第一个，则用 pre_e 返回其前驱，否则失败，pre_e 无定义
    NextElem(L, cur_e, &next_e);
      初始条件：线性表 L 存在
      操作结果：若 cur_e 是 L 中的元素，且不是最后一个，则用 next_e 返回其后驱，否则失败，next_e 无定义
    ListInsert(&L, i, e);
      初始条件：线性表 L 存在，且 1 <= i <= ListLength(L) + 1
      操作结果：在 L 中第 i 个元素前插入新的元素 e，L 的长度加 1
    ListDelete(&L, i, &e);
      初始条件：线性表 L 存在且非空，且 1 <= i <= ListLength(L)
      操作结果：删除 L 中的第 i 个元素并用 e 返回其值，L 的长度减 1
    TraverseList(L);
      初始条件：线性表 L 存在
      操作结果：对线性表进行遍历，在遍历过程中对每个结点访问一次，遍历过程中调用 vi() 操作元素
  }
```

## coding

```c
#include "pre_need.h"
#include <stdio.h>
#include <stdlib.h>

#define LIST_INIT_SIZE 100 // Initialization
#define LISTINCREMENT 10   // increment
typedef int ElemType;

typedef struct{
  ElemType *elem;
  int length;
  int listsize;
} SqList;

Status InitList_Sq(SqList *L){
  L->elem = (ElemType *)malloc(LIST_INIT_SIZE * sizeof(ElemType)); // Create an empty list
  if(!L->elem) exit(OVERFLOW);      // Storage allocation failure
  L->length = 0;                    // The empty table length is 0
  L->listsize = LIST_INIT_SIZE;     // Initial storage capacity
  return OK;
}

Status DestroyList_Sq(SqList *L){
  if(!L->elem) return ERROR;
  free(L->elem);
  L->elem = NULL;
  L->length = 0; // important
  L->listsize = 0; // important
  return OK;
}

Status ClearList_Sq(SqList *L){
  int i;
  if(!L->elem) return ERROR;
  for(i=0;i<L->length;i++) L->elem[i] = 0;
  L->length = 0; // important
  return OK;
}

Status ListEmpty_Sq(SqList L){
  if(!L.elem) return ERROR;
  if(L.length == 0) return TRUE;
  else return FALSE;
}

int ListLength_Sq(SqList L){
  if(!L.elem) return ERROR;
  return L.length;
}

Status GetElem_Sq(SqList L,int i, ElemType *e){
  if(!L.elem) return ERROR;
  if(i < 1 || i > L.length+1) return ERROR; // i is Wrongful
  *e = L.elem[i-1];
  return OK;
}

int LocateElem_Sq(SqList L,ElemType e,Status (*compare)(ElemType,ElemType)){
  // the first element match compare() , if find ,will return index or 0
  int i = 1;
  ElemType *p = L.elem;
  while(i<L.length && !(*compare)(*p++,e)) ++i;
  if(i<=L.length) return i;
  else return 0;
}

Status compare(ElemType e1, ElemType e2) {
    if (e1 == e2) {
        return TRUE;
    } else {
        return FALSE;
    }
}

Status PriorElem_Sq(SqList L,ElemType cur_e,ElemType *pre_e){
  if(!L.elem) return ERROR;
  int i=2;
  ElemType *p = L.elem+1;
  while(i++ <= L.length && *p++ != cur_e);
  if(i>L.length) return INFEASIBLE;
  else{
    *pre_e = *--p;
    return OK;
  }
}
Status NextElem_Sq(SqList L,ElemType cur_e,ElemType *next_e){
  if(!L.elem) return ERROR;
  int i=1;
  ElemType *p = L.elem+1;
  while(i++ < L.length && *p++ != cur_e);
  if(i>L.length) return INFEASIBLE;
  else{
    *next_e = *++p;
    return OK;
  }
}

Status ListInsert_Sq(SqList *L, int i,ElemType e){
  if(i < 1 || i > L->length+1) return ERROR; // i is Wrongful
  if(L->length>=L->listsize){ // Current storage space is full, increase allocation
    ElemType * newbase;
    newbase = (ElemType *)realloc(L->elem,(L->listsize+LISTINCREMENT)*sizeof(ElemType));
    if(!newbase) exit(ERROR); // Storage allocation failure
    L->elem = newbase;         // New base address
    L->listsize += LISTINCREMENT; // increase storage capacity
  }
  int *q = &(L->elem[i-1]);
  for(int *p=&(L->elem[L->length-1]);p>=q;--p) *(p+1) = *p;
  *q = e;
  ++L->length;
  return OK;
}

Status ListDelete_Sq(SqList *L,int i,ElemType *e){
  if(i<1||i>L->length) return ERROR; // i is Wrongful
  int *p = &(L->elem[i-1]);
  e = p;
  int *q=L->elem+L->length-1;
  for(++p;p<=q;++p) *(p-1) = *p;
  --L->length;
  return OK;
}

Status ListTraverse(SqList L,void (* visit)(ElemType*)){
  ElemType *p = L.elem;
  int i;
  for(i=1;i<=L.length;i++){
    visit(p++);
  }
  return OK;
}

void vi(ElemType *e) {
    printf("%d ", *e);
}

void MergeList_Sq(SqList La,SqList Lb,SqList * Lc) {
  ElemType *pa,*pb,*pc,*pa_last,*pb_last;
  pa = La.elem;
  pb = Lb.elem;
  Lc->listsize = Lc->length = La.length + Lb.length;
  Lc->elem = (ElemType *)malloc(Lc->listsize * sizeof(ElemType));
  if(!Lc->elem) exit(OVERFLOW);
  pa_last = La.elem + La.length -1;
  pb_last = Lb.elem + Lb.length -1;
  while (pa<=pa_last&&pb<=pb_last) {
    if(*pa<=*pb) *pc++ = *pa++;
    else *pc++ = *pb++;
  }
  while(pa<=pa_last) *pc++ = *pa++;
  while(pb<=pb_last) *pc++ = *pb++;
}

Status equal(ElemType c1, ElemType c2)
{ /* 判断是否相等的函数，Union()用到 */
	if (c1 == c2)
		return TRUE;
	else
		return FALSE;
}

void  union_Sq(SqList *La,SqList Lb) {
  ElemType e;
  int La_len,Lb_len,i;
  La_len = ListLength_Sq(*La);
  Lb_len = ListLength_Sq(Lb);
  for(i=1;i<=Lb_len;i++){
    GetElem_Sq(Lb,i,&e);
    if(!LocateElem_Sq(&La,e,equal))
    ListInsert_Sq(La,++La->length,e);
  }
}


int main(int argc, char const *argv[]) {
  /* code */
  return 0;
}

```
