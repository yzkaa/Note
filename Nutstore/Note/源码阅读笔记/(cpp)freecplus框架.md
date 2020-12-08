入手点：freecplus.h和freecplus.cpp

# freecplus.cpp

```cpp
void *memset(void *str,int c,size_t n)//#include<string.h>
```

memset将字符c（无符号字符）赋值到参数str所指向的字符串的前n个字符。

- **str** -- 指向要填充的内存块。
- **c** -- 要被设置的值。该值以 int 形式传递，但是函数在填充内存块时是使用该值的无符号字符形式。
- **n** -- 要被设置为该值的字符数。

```c++
//一个安全的strcpy函数
char *STRCPY(char* dest,const size_t destlen.const char* src){
	if(dest==0) return 0;
    memset(dest,0,destlen);	//初始化dest
    if(src==0) return dest;
    
    if(strlen(src)>destlen-1) strncpy(dest,src,destlen-1);
    else strcpy(dest,src);
    
    return dest;
}
```

