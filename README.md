# **AItlas**

AItlas is a knowledge graph designed to go with Loki NLU system.



## 1.  Methodology

### 1.1 Wiki Person 

#### 1.1.1 zhwiki_abstract_2306/
將 [維基百科](https://www.wikipedia.org/) 中所有中文條目文本的「序言」(Abstract) (待修正) 分別取出以下方 .json 的格式存放在 zhwiki_abstract_2306 目錄中。 
```txt
克律塞伊斯
克律塞伊斯（古希臘語：Χρυσηΐς）。[1]古希臘神話人物之一。其事跡見於《伊里亞特》。為特洛伊阿波羅祭司克律塞斯之女，為阿伽門農所俘。後者沉迷於其美貌而拒絕其父贖回，遂遭太陽神降瘟疫於希臘聯軍，使之迫於壓力而派奧德修斯將其送回。一說她與阿伽門農結合，生下一子。其形象於相關藝術作品中得到廣泛反映。
```

```json
{
    "id":"克律塞伊斯",
    "text2":"克律塞伊斯。克律塞伊斯（古希臘語：Χρυσηΐς）。[1]古希臘神話人物之一。其事跡見於《伊里亞特》。為特洛伊阿波羅祭司克律塞斯之女，為阿伽門農所俘。後者沉迷於其美貌而拒絕其父贖回，遂遭太陽神降瘟疫於希臘聯軍，使之迫於壓力而派奧德修斯將其送回。一說她與阿伽門農結合，生下一子。其形象於相關藝術作品中得到廣泛反映。"
}
```
#### 1.1.2 person_classifier.py
從 zhwiki_abstract_2306 下的所有文本中，以正規表示式 (regular expression) 依人名在句中常見的結構 (待修正)，篩選出與「人物」相關的文本存放在 data/people 中。

```python
personPatLIST = ["^{}\s?（[^名又a-zA-Z]+）",
                 "^{}\s?\([^名又a-zA-Z]+\)",
                 "^{}\s?（[^名又a-zA-Z]*）",
                 "^{}，[^a-zA-Z]+人\b",
                 "^{}，[^a-zA-Z]+人(?=[，。])"
                ]
```

#### 1.1.3 utterance_insert.py 
讀取 data/people 中的 .json 檔案，抽取文本中出現的所有動詞，依動詞將句子分類後，將含有標的動詞的句子批次送上 [Loki](https://api.droidtown.co/document/#Loki) (Linguisitcs-Oriented Keyword Interface)，根據 Loki 的運作原理，project 為 Wiki People，intent 為標的動詞，utterance 為動詞後的實詞數量，此時 Loki 專案中的 .ref 檔案應為以下 .json 格式。
```json
{
    "pinyin":"",                      # 標的動詞漢語拼音
    "0e":["utterance1","utterance2"], # 動詞後含有零個實詞的句子
    "1e":["utterance1","utterance2"]  # 動詞後含有一個實詞的句子
}
```

#### 1.1.4 pro_drop.py (待修正)
考慮到漢語中 pro-drop 現象頻繁在文本中出現的因素，將 data/people 中所有條目標題人物充當主詞填回序言中每個句子的句首位置。
```json
{"id": "克律塞伊斯", "text2": "克律塞伊斯。_克律塞伊斯_古希臘神話人物之一。克律塞伊斯事蹟見於《伊里亞特》。_克律塞伊斯_爲特洛伊阿波羅祭司克律塞斯之女，_克律塞伊斯_爲阿伽門農所俘。_克律塞伊斯_後者沉迷於其美貌而拒絕其父贖回，_克律塞伊斯_遂遭太陽神降瘟疫於希臘聯軍，克律塞伊斯使之迫於壓力而派奧德修斯將其送回。_克律塞伊斯_一說她與阿伽門農結合，克律塞伊斯生下一子。克律塞伊斯形象於相關藝術作品中得到廣泛反映"}
```



## 2. Contents

```python
├── LICENSE
├── README.md
├── data
│   ├── preprocessed
│   │   ├── stage01_lines  #原始資料裡拆出來的 lines
│   │   └── stage01_text   #原始資料裡拆出來的 text
│   └── wiki-pages #原始資料
└── tools
    ├── titleJointer.py    #拆出原始資料的工具
    ├── extract.py (script to extract wikipedia title and summary from topic.txt
    └── topic.txt (txt file containing wikipedia article names)
```