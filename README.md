# Make the Word Cloud #1
## amueller의 wordcloud package를 이용한 mask를 활용한 wordcloud 만들기! 
![윤동주시인의 워드클라우드](https://github.com/kimyoungjin06/wordcloud/blob/master/Yun_Dongju.png "참 쉽죠? :)")

#### 참고:
#### [1] https://github.com/amueller/word_cloud
#### [2] http://minimaxir.com/2016/05/wordclouds/
#### [3] https://github.com/minimaxir/stylistic-word-clouds/blob/master/wordcloud_github.py

## 1. Package import & font, mask, message setup!
### 이 코드는 mask를 다루는 부분, font를 다루는 부분, word를 다루는 부분으로 나뉜다. 먼저 필요한 패키지들을 불러오는데, numpy와 random, palettable은 font 색 설정할 때, PIL은 mask 이미지를 처리할 때, wordcloud는 이 모든 것을 이용해서 word cloud를 그릴 때 이용된다.
### 아래에서 딱히 어려운 점은 없고, color_func가 여기서는 Dark2_8이라는 palette를 이용해서 색을 만들어낸다. Palette를 바꾸고 싶다면 이 부분을 바꾸면 될 것!
### 여기서 사용한 example은 윤동주 시인의 작품 전체와, 궁서체의 '동주'라는 (1024, 512) pixel의 이미지를 사용하였다.
#### *PIL은 python3에서는 Pillow로 설치하고 PIL로 사용하면 된다.
#### *mask로 쓸 이미지는 png파일로 우리가 단어를 배치할 공간만 그림이 존재해야 한다. 나머지 공간엔 흰색이 아니라 아예 이미지가 없는 png파일이 필요하다.


```python
import numpy as np
import random
from PIL import Image
from wordcloud import WordCloud, STOPWORDS
from palettable.colorbrewer.qualitative import Dark2_8

def color_func(word, font_size, position, orientation, random_state=None, **kwargs):
    return tuple(Dark2_8.colors[random.randint(0,7)])
font = "Hunf"
font_path = "%s.ttf" % font

icon = "Mask_YDJ"
icon_path = "%s.png" % icon

f = open("poem_YDJ.txt", 'r')
message = f.read()
print(message)
f.close()
```


## 2. Load the mask, font coloring, generate word cloud!
### 먼저 icon에 image를 불러오고, mask를 RGB type으로 icon과 같은 사이즈로 전부 (255,255,255)값 (아마 흰색?)으로 만들어내고, 이 (255,255,255)가 이미지가 채워지지 않는 부분을 의미한다.  (?Image.new를 참고!), 그리고 나서 이 mask에 아까 불러온 icon의 형상을 붙여넣는다. 그리고 나서 이를 nparray로 만든다.
### 이제 만들어놓은 mask와 불러놨던 font를 이용하여 WordCloud함수를 이용해 wordcloud를 만들어내고, WordCloud.recolor를 이용해서 아까 만들어놓은 color_func()를 사용해 불러온 palette를 이용해 색을 입힌다. 그리고 저장하면 끝!


```python
icon = Image.open(icon_path).convert("RGBA")
mask = Image.new("RGB", icon.size, (255,255,255))
mask.paste(icon,icon)
mask = np.array(mask)

wc = WordCloud(font_path=font_path, background_color="white", max_words=2000, mask=mask,
               max_font_size=300, random_state=42)
               
# generate word cloud
wc.generate_from_text(message)
wc.recolor(color_func=color_func, random_state=3)
wc.to_file("Yun_Dongju.png")
```




    <wordcloud.wordcloud.WordCloud at 0x7f84df397780>



### Additional Tip. [2]에 가면, 올린이의 다양한 작품들을 볼 수 있다. 그리고 해당하는 코드들은 [3]에서 볼 수 있다. [1]에서는 wordcloud의 기본적 사용법들에 대해 알려준다. 참고하기를


# Make Word Cloud with color mask and word frequency

## Add coloring 

## Data structure is json

```json
# Structure of word_dict
{
word1 : count,
word2 : count,
...
word_N : count
}
```

# Full code

```python
# IMPORT PACKAGES
import numpy as np
import random
from PIL import Image
from wordcloud import WordCloud, STOPWORDS, ImageColorGenerator
from palettable.colorbrewer.qualitative import Dark2_8

# FONT, ICON, MASK
font = "a엄마의편지B"
font_path = "%s.ttf" % font

icon = "korea2"
icon_path = "%s.png" % icon

icon = Image.open(icon_path).convert("RGBA")
mask = Image.new("RGB", icon.size, (255,255,255))
mask.paste(icon,icon)
mask = np.array(mask)

# Word Cloud with colored mask
wc = WordCloud(font_path=font_path, background_color="white", max_words=20000, mask=mask,
               max_font_size=300, random_state=42)

coloring = np.array(Image.open(icon_path))
image_colors = ImageColorGenerator(coloring)
image_colors.default_color = [0.6,0.6,0.6] # Important!!! at 2018.07.07

# Generate word cloud
wc.generate_from_frequencies(word_dict)
wc.recolor(None,image_colors)
wc.to_file("output.png")
```

![colored_mask](https://github.com/kimyoungjin06/wordcloud/blob/master/colored_mask.png "참 쉽죠? :)")
