C 语言程序设计课后题

##### 第一章

1\. 

① 允许直接访问物理地址，可以直接对硬件进行操作

贴近底层，执行效率高

② 代码级别跨平台

③ C 语言传递参数，既可以指针传递，又可以值传递

2\. 

```c
#include <stdio.h>

int main(void){
    
    printf("This is my first Program!");
    
    return 0;
}
```

3\. 

```c
#include <stdio.h>

int main(void){
    int i = 0;
    for(i = 1; i <= 4; i++){
        printf("****");
    }
    return 0;
}
```

4\. 

```c
#include <stdio.h>

int main(void){
   	int i, j, k;
	for (i = 1; i <= 3; i++) {
		for (j = i - 1; j > 0; j--) {
			printf(" ");
		}
		for (k = 5 - ((i - 1) * 2); k > 0;k--) {
			printf("*");
		}
		printf("\n");
	}
	return 0;
}
```

5\. 略

6\. 

```c
#include <stdio.h>

int main(void){
	int a, b, sum;
	printf("请输入两个整数中间用空格隔开：");
   	scanf("%a %b:",&a, &b);
	sum = a + b;
	printf("两个整数的和为：%d", sum);
	return 0;
}
```

7\.

```c
#include <stdio.h>

int main(void){
    int a, b, max;
 	scanf("%d %d", &a, &b);   
    max = a > b ? a : b;
    return 0;
    
}
```

##### 第二章

2.

```c
	int length, width, area, sum;
	printf("请输入长方形的长和宽：");
	scanf_s("%d %d", &length, &width);
	area = length * width;
	sum = (area + length) * 2;
	printf("长方形的面积为 %d,周长为%d", area, sum);
```

3\.

```c
#include <stdio.h>
#include <math.h>

#define PI 3.14159

int main(void){
    double v = 4/3*PI* pow(4.3, 3.0);
    printf("v=%.4lf", v);
}

```

4\. 

```c
double r;
scanf("%lf", &r);
double v = 4 / 3 * PI * pow(r, 3.0);
printf("v=%.4lf", v);

```

5\.

```c
	double height;
	double second;
	scanf_s("%lf", &second);
	height = 0.5 * 10 * pow(second, 2.0);
	printf("height=%.3f", height);
```

9\.

```c
# 整数除以整数截断小数
	int num = 0, size = 0;
	scanf_s("%d", &num);
	while (num > 0) {
		num /= 10;
		size++;
	}
	printf("位数为%d", size);
```

```
int num = 0, count = 10;
scanf_s("%d", &num);
if(num / 10 == 0){
	printf("1");
	return 0;
}
if(num / 100 == 0){
	printf("2");
	return 0;
}
if(num / 1000 == 0){
	printf("3");
	return 0;
}

if(num / 10000 == 0){
	printf("4");
	return 0;
}
if(num / 100000 == 0){
	printf("5");
	return 0;
}

```

10\.

```c
# include <math.h>

double x, sum;
sum = 0;
scanf_s("%lf", &x);
sum = 15 + x / 2 - (5 + x) - log(x);
printf("%.2f", sum);
```

##### 第三章

1\.

```c
	int a, b, c;
	scanf_s("%d%d%d", &a, &b, &c);
	if (a > b) {
		a ^= b;
		b ^= a;
		a ^= b;
	}
	if (b > c) {
		b ^= c;
		c ^= b;
		b ^= c;
	}
	if (a > b) {
		a ^= b;
		b ^= a;
		a ^= b;
	}
	printf("a=%d, b=%d, c=%d", a, b, c);
	return 0;
```

2\.

```c
int n;
scanf("%d", n):
if(n % 2 == 0){
    printf("偶数");
}else{
    printf("奇数");
}
```

3\.

```c
	int num = 0;
	int g, s, b;
	scanf_s("%d", &num);
	g = num % 10;
	s = num / 10 % 10;
	b = num / 100;
	if(num == pow(g, 3)+ pow(s, 3)+ pow(b,3)){
		printf("整数%d是水花仙数", num);
	}else{
		printf("整数%d不是水花仙数", num);
	}
```

4\.

```c
	int side1, side2, side3;
	printf("请输入三角形三边的长：");
	scanf_s("%d %d %d", &side1, &side2, &side3);
	if (side1 + side2 > side3 || side1 + side3 > side2 || side2 + side3 > side1) {
		printf("yes");
	} else {
		printf("no");
	}
```

5.

```c
	char ch;
	printf("请输入字符");
	ch = getchar();
	if (ch >= 'a' && ch <= 'zdasdf') {+++++++ 
		ch ^= 0x20;
	}
	printf("%c", ch);
```

6\. 

```c
char ch;
	int index = 0;
	printf("请输入字符");
	ch = getchar();
	if (ch >= 'a' && ch <= 'z') {
		index = ch - 96;
	}
	if (ch >= 'A' && ch <= 'Z') {
		index = ch - 64;
	}
	printf("字母%c是第%d个字母", ch, index);
```

7\.

```c
	int year = 0;
	printf("请输入年份：");
	scanf_s("%d", &year);
	int month = 0;
	printf("请输入月份：");
	scanf_s("%d", &month);

	switch (month) {
	case 1:
	case 3:
	case 5:
	case 7:
	case 8:
	case 10:
	case 12:
		printf("改月天数为 31 天");
		break;
	case 2:
		if ((year % 4 == 0 && year % 100 != 0) || year % 400 == 0) {
			printf("29");
		}
		else {
			printf("28");
		}
		break;
	case 4:
	case 6:
	case 9:
	case 11:
		printf("30");
		break;
	}
```

8\.