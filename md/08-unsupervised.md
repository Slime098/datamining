
キーワード：教師なし学習

## 主成分分析

キーワード：主成分分析

キーワード：主成分スコア

キーワード：バイプロット

キーワード：標準化

```r
library("devtools")
install_github("vqv/ggbiplot")
```

```r
my_data <- data.frame(
  language = c(  0, 20, 20, 25, 22, 17),
  english  = c(  0, 20, 40, 20, 24, 18),
  math     = c(100, 20,  5, 30, 17, 25),
  science  = c(  0, 20,  5, 25, 16, 23),
  society  = c(  0, 20, 30,  0, 21, 17))
row.names(my_data) <- c("A", "B", "C", "D", "E", "F")
```

```r
my_result <- prcomp(my_data)              # データを標準化しない場合
# my_result <- prcomp(my_data, scale = T) # データを標準化する場合1
# my_result <- prcomp(scale(my_data))     # データを標準化する場合2
my_result$x                               # 主成分スコア
```

```r
summary(my_result) # 累積寄与率
```

```r
# biplot(my_result, scale = 0) # 「scale = 0」は解説どおりにするためのオプション
ggbiplot::ggbiplot(pcobj = my_result, labels = row.names(my_data), scale = 0)
```

```r
my_result$rotation # 主成分の係数ベクトル
```

**練習：`USArrests`という名前でRに格納されているデータを主成分分析し，バイプロットを描いてください．データの意味は次のとおりです．**

|ラベル|意味|
|--|--|
|Murder|殺人の逮捕件数（人口10万人あたり）|
|Assault|暴行の逮捕件数（人口10万人あたり）|
|UrbanPop|都市部の人口率|
|Rape|レイプの逮捕件数（人口10万人あたり）|


```r
my_data <- USArrests
tmp <- read.csv("https://raw.githubusercontent.com/taroyabuki/datamining/master/data/state_abbreviations.csv")
row.names(my_data) <- tmp$abbreviation
head(my_data)
```

### 補足：主成分分析の具体的な計算

```r
my_data <- data.frame(
  language = c(  0, 20, 20, 25, 22, 17),
  english  = c(  0, 20, 40, 20, 24, 18),
  math     = c(100, 20,  5, 30, 17, 25),
  science  = c(  0, 20,  5, 25, 16, 23),
  society  = c(  0, 20, 30,  0, 21, 17))
row.names(my_data) <- c("A", "B", "C", "D", "E", "F")

my_raw_data <- as.matrix(my_data) # データを格納した行列
```

#### 標準化をしない場合

```r
my_matrix <- var(my_raw_data)                      # 分散共分散行列
(my_eigen <- eigen(my_matrix))                     # 固有値と固有ベクトル

cumsum(my_eigen$values / sum(my_eigen$values))     # 累積寄与率

scale(my_raw_data, scale = F) %*% my_eigen$vectors # 主成分スコア
```

#### 標準化をする場合

```r
my_matrix <- cor(my_raw_data)                  # 相関行列
(my_eigen <- eigen(my_matrix))                 # 固有値と固有ベクトル

cumsum(my_eigen$values / sum(my_eigen$values)) # 累積寄与率

scale(my_raw_data) %*% my_eigen$vectors        # 主成分スコア
```

## クラスター分析

キーワード：クラスター分析，クラスター

### 距離

キーワード：距離

キーワード：ユークリッド距離

|名前|指定方法|
|--|--|
|ユークリッド距離|`euclidean`|
|マンハッタン距離|`manhattan`|
|最大距離|`maximum`|
|キャンベラ距離|`canberra`|
|ミンコウスキー距離|`minkowski`|
|バイナリ距離|`binary`|


キーワード：マンハッタン距離

```r
name <- c("A", "B", "C", "D")
x    <- c(  0, -16,  10,  10)
y    <- c(  0,   0,  10, -15)
my_data <- data.frame(x, y, row.names = name)
```

```r
(my_dist <- dist(my_data, method = "euclidian")) # dist(my_data)でもよい．
```

**練習：点同士のマンハッタン距離を計算してください．**

### 階層的クラスター分析

キーワード：階層的クラスター分析

キーワード：デンドログラム

|指定法|名称|説明|
|--|--|--|
|`ward.D2`|ウォード法|クラスターの分散比を最大にする．|
|`single`|最短距離法|個体間の最小距離をとる．|
|`complete`|最長距離法|個体間の最大距離をとる（デフォルト）．|
|`average`|群平均法|個体間の距離の平均をとる．|
|`mcquitty`|McQuitty法|AとBがクラスターCになったとき，クラスターCとクラスターDの距離をAD，BDから決める．|


```r
# データを用意する．
name <- c("A", "B", "C", "D")
x    <- c(  0, -16,  10,  10)
y    <- c(  0,   0,  10, -15)
my_data <- data.frame(x, y, row.names = name)

# 距離を計算する．
my_dist <- dist(my_data, method = "euclidian")

# 階層的クラスター分析を行う．
my_result <- hclust(my_dist, method = "complete") # hclust(my_dist)でもよい．

# デンドログラムを描く．
plot(my_result, hang = -1)
rect.hclust(my_result_e, k = 3) # クラスター数が3の場合を赤線で示す．

# クラスター数が3の場合，各点がどのクラスターに属しているかを確認する．
(my_cluster <- cutree(my_result, k = 3))
```

**練習：マンハッタン距離とウォード法を使って，階層的クラスター分析を行ってください．**

**練習：主成分分析で使った試験結果のデータで，階層的クラスター分析を行ってください．**

```r
my_data <- data.frame(
  language = c(  0, 20, 20, 25, 22, 17),
  english  = c(  0, 20, 40, 20, 24, 18),
  math     = c(100, 20,  5, 30, 17, 25),
  science  = c(  0, 20,  5, 25, 16, 23),
  society  = c(  0, 20, 30,  0, 21, 17))
row.names(my_data) <- c("A", "B", "C", "D", "E", "F")
```

**練習：`USArrests`という名前でRに格納されているデータで，階層的クラスター分析を行い，デンドログラムを描いてください．**

```r
my_data <- USArrests
tmp <- read.csv("https://raw.githubusercontent.com/taroyabuki/datamining/master/data/state_abbreviations.csv")
row.names(my_data) <- tmp$abbreviation
head(my_data)
```

**練習：MASS::Animalsは，動物の体と脳の重量のデータです．このデータの動物をクラスター分析し，クラスター数を8にした場合に，馬（Horse）と同じクラスターに入る動物を挙げてください．**

```r
my_data <- MASS::Animals
my_dist <- dist(my_data)
my_result <- hclust(my_dist)
my_cluster <- cutree(my_result, k = 8)
names(my_cluster)[my_cluster == my_cluster["Horse"]]
```

### 階層的クラスター分析とヒートマップ

キーワード：ヒートマップ

```r
my_data <- data.frame(
  language = c(  0, 20, 20, 25, 22, 17),
  english  = c(  0, 20, 40, 20, 24, 18),
  math     = c(100, 20,  5, 30, 17, 25),
  science  = c(  0, 20,  5, 25, 16, 23),
  society  = c(  0, 20, 30,  0, 21, 17))
row.names(my_data) <- c("A", "B", "C", "D", "E", "F")

gplots::heatmap.2(x = as.matrix(scale(my_data)), # 標準化してみる．
                  distfun = function(m) { dist(m, method = 'euclidean') },
                  hclustfun = function(d) { hclust(d, method = 'complete') },
                  cexRow = 1, # 行ラベルのサイズ
                  cexCol = 1  # 列ラベルのサイズ
                  )
```

### 非階層的クラスター分析

キーワード：非階層的クラスター分析

キーワード：k-mean法

```r
# データを用意する．
name <- c("A", "B", "C", "D")
x    <- c(  0, -16,  10,  10)
y    <- c(  0,   0,  10, -15)
my_data <- data.frame(x, y, row.names = name)

# クラスター数を3として，非階層的クラスター分析を行う．
my_result <- kmeans(x = my_data, centers = 3)

# 各データがどのクラスターに属しているかを確認する．
(my_cluster <- my_result$cluster)
```

**練習：主成分分析で使った試験結果のデータで，非階層的クラスター分析を行ってください．ただし，クラスター数は2とします．**

```r
my_data <- data.frame(
  language = c(  0, 20, 20, 25, 22, 17),
  english  = c(  0, 20, 40, 20, 24, 18),
  math     = c(100, 20,  5, 30, 17, 25),
  science  = c(  0, 20,  5, 25, 16, 23),
  society  = c(  0, 20, 30,  0, 21, 17))
row.names(my_data) <- c("A", "B", "C", "D", "E", "F")
```

**練習：`USArrests`という名前でRに格納されているデータで，非階層的クラスター分析を行ってください．ただし，クラスター数は4とします．**

```r
my_data <- USArrests
tmp <- read.csv("https://raw.githubusercontent.com/taroyabuki/datamining/master/data/state_abbreviations.csv")
row.names(my_data) <- tmp$abbreviation
head(my_data)
```

**練習：MASS::Animalsは，動物の体と脳の重量のデータです．このデータの動物をクラスター分析し，クラスター数を6にした場合に，人間と同じクラスターに入る動物を挙げてください．**

```r
my_data <- MASS::Animals
my_result <- kmeans(x = my_data, centers = 6)
my_cluster <- my_result$cluster
names(my_cluster)[my_cluster == my_cluster["Human"]]
```

### 主成分分析とクラスター分析

```r
my_data <- USArrests
tmp <- read.csv("https://raw.githubusercontent.com/taroyabuki/datamining/master/data/state_abbreviations.csv")
row.names(my_data) <- tmp$abbreviation

my_cluster <- cutree(hclust(dist(scale(my_data))), k = 4)
# my_cluster <- kmeans(scale(my_data), centers = 4)$cluster

my_result <- prcomp(my_data, scale = T)
ggbiplot::ggbiplot(pcobj = my_result,
                   labels = row.names(my_data),
                   scale = 0,
                   groups = as.factor(my_cluster),
                   ellipse = T)
```
