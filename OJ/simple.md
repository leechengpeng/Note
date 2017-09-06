## 1. 判断素数/质数
```C++
bool prime(unsigned vNum)
{
	unsigned UpBound = sqrt(vNum) + 1;
	for (size_t i = 2; i < UpBound; i++)
	{
		if (vNum % i == 0) { return false; }
	}

	return true;
}
```
参考：http://www.cnblogs.com/xiehongfeng100/p/4332998.html
