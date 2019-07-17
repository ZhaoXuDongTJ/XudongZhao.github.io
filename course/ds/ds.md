# Summary
This is my learning experience.
# Let's Go!
![](images/data.jpg)
![](images/List ADT.jpg)
Give an example:                      
- A=AUA
```
void union(List &La, List Lb){
  La_len = ListLength(La);
  La_len = ListLength(Lb);
  for(i = 1; i < Lb_len; i++){
    GetElem(Lb,i,e);
    if(!Location(La, e, equal)){
      ListInsert(La, ++La_len, e);
    }
  }
}
```
- Merge List: Lc = La and Lb
