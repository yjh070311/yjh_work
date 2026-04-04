dict([一系列元组])
.get(key,default)如果查找不到返回default
.keys,.values分别返回包含字典中所有键和值的视图对象
视图对象只是一种无需创建独立数据副本即可查看词典内容的方法。
.items()方法返回一个视图对象，包含字典中所有键值对，包括键和值
.clear消除
.pop移除你指定为第一个参数的键值对，并返回其值。如果key不存在，它会返回你指定的第二个参数的默认值
.update（新dic）有覆盖，无加上，
pizza.update({ 'price': 15, 'total_time': 25 })
for product in enumerate(products):
    print(product)
返回（index，product）元组
enu（products.items())
返回索引＋元组
