# N+1 Query
在 ORM 架構下常見問題。可使用 JOIN 解決

# ORDER BY RAND()
在大型表格中隨機取出一筆資料的效能問題

```
SELECT * FROM products ORDER BY RAND() LIMIT 1;
```
這樣做會導致 mysql 生成 n 個亂數並且排序後取出一個。

假設存在一個欄位 c 是平均分佈，只要生成一個亂數 x 介於 min(c) 到 max(c)，並取出 c >= x ，對 C 欄位排序後的第一筆，就可以快速產生。

# 加權 RAND 的做法

利用上面的方法，讓他的值不是平均分布，而是你希望的分布。

利用累進的概念，比方說：

稀有度 | 累進值
------|--------
SSR   | 1
SR    | 5
R     | 20
N     | 100

可以取一個介於 1 到 100 的亂數，從上表中找出累進值 >= 亂數的值，這樣做的話就會發現結果跟存了 100 個 row 相同。

- 亂數介於  0 到   1 代表抽到 SSR
- 亂數介於  2 到   5 代表抽到 SR
- 亂數介於  5 到  20 代表抽到 R
- 亂數介於 20 到 100 代表抽到 N

# LIMIT n, 20

LIMIT 100000, 20 時 mysql 會找出 100000 筆資料，然後傳回最後 20 筆
超慢

可以使用 id 或 timestamp 的值，表示讀到哪一頁，下一頁就可以從那個頁面繼續讀
不要讓 user 直接輸入頁面數做查詢

例子：
http://blog.gslin.com/search?updated-max=2012-06-24T23:15:00%2B08:00&max-results=10