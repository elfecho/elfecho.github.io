## 用字典映射代替switch case语句

比如JavaScript里使用的switch

```javascript
day = 1
switch(day) {
	case 0:
		dayName = 'Sunday'
		break
	case 1:
		dayName = 'Monday'
		break
	case 2:
		dayName = 'Thesday'
		break
	...
	default:
		dayName = 'Unkown'
		break
}
```

python中由于没有switch方法，我们可以使用字典的映射来代替这个方法

```python
day = 6

switcher = {
    0: 'Sunday',
    1: 'Monday',
    2: 'Tuesday'
}

day_name = switcher.get(day, 'Unkown')

  

print(day_name)
```