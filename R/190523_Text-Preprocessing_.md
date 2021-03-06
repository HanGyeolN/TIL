# Text Preprocessing


``` r
# 최대 단어수 기준으로 문장 x 단어 행렬 구성
#        word1   word2 ...
# sent1    R      is   ...
# sent2   blah   blah  ...
#  ...

max.length.sentences<-max(length.sentences) # 
#최대 단어 개수만큼, " "를 구분자로, matrix로 쪼갠다.

sent.word.matrix<-(str_split_fixed(unlist(r_wiki_sent), " ", max.length.sentences))

mydata<-data.frame(sent.word.matrix)

colnames(mydata)<-paste("word",1:max(length.sentences),sep="")
rownames(mydata)<-paste("sent",1:length(length.sentences),sep="")
mydata
```

```R
##        word1   word2    word3       word4    word5 word6      word7
## sent1      R      is        a programming language   and   software
## sent2    The       R language          is   widely  used      among
## sent3 Polls, surveys       of        data  miners,   and    studies
## sent4      R      is        a         GNU  package                 
## sent5    The  source     code         for      the     R   software
## sent6      R      is   freely   available    under   the        GNU
## sent7  While       R      has           a  command  line interface,
##               word8     word9      word10    word11       word12
## sent1   environment       for statistical computing          and
## sent2 statisticians       and        data    miners          for
## sent3            of scholarly  literature databases         show
## sent4                                                           
## sent5   environment        is     written primarily           in
## sent6       General    Public    License,       and pre-compiled
## sent7         there       are     several graphical   front-ends
##           word13      word14     word15   word16    word17        word18
## sent1   graphics   supported         by      the         R    Foundation
## sent2 developing statistical   software      and      data      analysis
## sent3       that         R's popularity      has increased substantially
## sent4                                                                   
## sent5         C,    Fortran,        and        R                        
## sent6     binary    versions        are provided       for       various
## sent7 available.                                                        
##          word19      word20    word21
## sent1       for Statistical Computing
## sent2                                
## sent3        in      recent    years.
## sent4                                
## sent5                                
## sent6 operating     systems          
## sent7
```

``` r
paste("word",1:max(length.sentences),sep="") # O: character vector
```

    ##  [1] "word1"  "word2"  "word3"  "word4"  "word5"  "word6"  "word7" 
    ##  [8] "word8"  "word9"  "word10" "word11" "word12" "word13" "word14"
    ## [15] "word15" "word16" "word17" "word18" "word19" "word20" "word21"


#### Split

```R
> mysentence<-"Learning R is so interesting"
> strsplit(mysentence, split=" ")
[[1]]
[1] "Learning"    "R"           "is"          "so"          "interesting"

strsplit(rWikiPara[[1]], split="\\. ") -> rWikiSent
strsplit(rWikiSent[[1]], split=" ") -> rWikiWord
strsplit(rWikiWord[[1]], split="") -> rWikiLett
```

- 문자열 쪼개기



#### Collapse

```R
> a<-c("a","b")
> a
[1] "a" "b"
> paste(a,collapse = "")
[1] "ab"
```

- 문자열 합치기

## regexpr

```R
> gregexpr('ing', mySent)
[[1]]
[1]  6 25
attr(,"match.length")
[1] 3 3
attr(,"index.type")
[1] "chars"
attr(,"useBytes")
[1] TRUE
```

- 특정 문자열 찾기
- 특정 문자열이 시작되는 attributes가 들어있는 index를 반환한다.

#### 문자열의 시작과 끝 찾기

```R
loc.begin<-as.vector(regexpr('ing',mySent))
loc.length<-attr(regexpr('ing',mySent),"match.length")
loc.end<-loc.begin+loc.length-1
```



## stringr 문서 참고하기

https://stringr.tidyverse.org/

https://github.com/rstudio/cheatsheets/blob/master/strings.pdf 
치트시트 참고하자.



#### 특정 문자 추출하기

``` r
str_extract_all(r_wiki, "software environment")
```

    ## [[1]]
    ## [1] "software environment" "software environment"

#### 출력 결과 옵션 바꾸기
``` r
str_extract_all(r_wiki, "software environment", simplify = TRUE)
```

    ##      [,1]                   [,2]                  
    ## [1,] "software environment" "software environment"

#### -----------------------------------

#### 조건에 맞는 문자열 추출하기

``` r
# 앞글자가 대문자인것 추출
str_extract_all(r_wiki, "[[:upper:]]")
```

    ## [[1]]
    ##  [1] "R" "R" "F" "S" "C" "T" "R" "P" "R" "R" "G" "N" "U" "T" "R" "C" "F"
    ## [18] "R" "R" "G" "N" "U" "G" "P" "L" "W" "R"

``` r
# 대문자가 2개 이어진걸 추출하고싶다.
str_extract_all(r_wiki, "[[:upper:]]{2}")
```

    ## [[1]]
    ## [1] "GN" "GN"





#### 첫글자가 대문자로 시작되는 단어들을 추출하고 싶다.

``` r
str_extract_all(r_wiki, "[[:upper:]]{1}[[:alpha:]]{0,}") 
# [[알파벳 중에서]]{최소0개, 최대 n개 (없으면 무한)}
```

    ## [[1]]
    ##  [1] "R"           "R"           "Foundation"  "Statistical" "Computing"  
    ##  [6] "The"         "R"           "Polls"       "R"           "R"          
    ## [11] "GNU"         "The"         "R"           "C"           "Fortran"    
    ## [16] "R"           "R"           "GNU"         "General"     "Public"     
    ## [21] "License"     "While"       "R"



####  대문자 1개 + 소문자 1개 이상인것

``` r
str_extract_all(r_wiki, "[[:upper:]]{1}[[:alpha:]]{1,}") 
# [[알파벳 중에서]]{최소1개}
```

    ## [[1]]
    ##  [1] "Foundation"  "Statistical" "Computing"   "The"         "Polls"      
    ##  [6] "GNU"         "The"         "Fortran"     "GNU"         "General"    
    ## [11] "Public"      "License"     "While"



### 대문자 1개 + 길이가 2~4인것

``` r

str_extract_all(r_wiki, "[[:upper:]]{1}[[:alpha:]]{1,3}") 
# [[알파벳 중에서]]{최소0개, 최대 3개}
```

```r
## [[1]]
##  [1] "Foun" "Stat" "Comp" "The"  "Poll" "GNU"  "The"  "Fort" "GNU"  "Gene"
## [11] "Publ" "Lice" "Whil"
```

### 추출해서 빈도수 확인

``` r

yextract <- str_extract_all(r_wiki, "[[:upper:]]{1}[[:alpha:]]{1,3}")
table(myextract)
```

    ## myextract
    ## Comp Fort Foun Gene  GNU Lice Poll Publ Stat  The Whil 
    ##    1    1    1    1    2    1    1    1    1    2    1



### 찾는 단어의 시작과 끝 인덱스 찾기

``` r

str_locate(r_wiki, "software environment")
```

    ##      start end
    ## [1,]    33  52



``` r
str_locate_all(r_wiki, "software environment")
```

    ## [[1]]
    ##      start end
    ## [1,]    33  52
    ## [2,]   464 483



### 첫글자가 대문자로 시작하는 단어위치를 알고싶다.

``` r

mylocate<-str_locate_all(r_wiki,"[[:upper:]]{1}[[:alpha:]]{0,}")
head(mylocate)
```

    ## [[1]]
    ##       start end
    ##  [1,]     1   1
    ##  [2,]   110 110
    ##  [3,]   112 121
    ##  [4,]   127 137
    ##  [5,]   139 147
    ##  [6,]   150 152
    ##  [7,]   154 154
    ##  [8,]   271 275
    ##  [9,]   358 358
    ## [10,]   418 418
    ## [11,]   425 427
    ## [12,]   438 440
    ## [13,]   462 462
    ## [14,]   509 509
    ## [15,]   512 518
    ## [16,]   525 525
    ## [17,]   528 528
    ## [18,]   560 562
    ## [19,]   564 570
    ## [20,]   572 577
    ## [21,]   579 585
    ## [22,]   665 669
    ## [23,]   671 671



### 조건에 맞는 문자열을 추출하고싶다.

``` r

myextract <- str_extract_all(r_wiki,"[[:upper:]]{1}[[:alpha:]]{0,}")
class(myextract[[1]])
```

    ## [1] "character"



#### 문자열 + index 합치기

``` r
# 찾은 문자열과 그 위치를 합치고싶다.
mydata$myword<-myextract[[1]]
mydata
```

    ##    start end      myword
    ## 1      1   1           R
    ## 2    110 110           R
    ## 3    112 121  Foundation
    ## 4    127 137 Statistical
    ## 5    139 147   Computing
    ## 6    150 152         The
    ## 7    154 154           R
    ## 8    271 275       Polls
    ## 9    358 358           R
    ## 10   418 418           R
    ## 11   425 427         GNU
    ## 12   438 440         The
    ## 13   462 462           R
    ## 14   509 509           C
    ## 15   512 518     Fortran
    ## 16   525 525           R
    ## 17   528 528           R
    ## 18   560 562         GNU
    ## 19   564 570     General
    ## 20   572 577      Public
    ## 21   579 585     License
    ## 22   665 669       While
    ## 23   671 671           R



### 고유명사 전처리 

``` r
# 고유명사 전처리를 하는 경우 (1개만)
str_replace(r_wiki,"software environment", "software_environmnet")
# 하지 않으면 전혀 관련없는 단어로 분리된다.
```



``` r
# 여러개를 전처리 하는 경우
temp<-str_replace_all(r_wiki, "software environment", "software_environment")
temp
```



### 여러 조건을 넣어주고 싶은 경우

``` r

str_extract_all(r_wiki,"software_environment|software|environment")
```

    ## [[1]]
    ## [1] "software"    "environment" "software"    "software"    "environment"

``` r
table(str_extract_all(r_wiki,"software_environment|software|environment"))
```

    ## 
    ## environment    software 
    ##           2           3

``` r
# 
str_extract_all(temp,"software_environment|software|environment")
```

    ## [[1]]
    ## [1] "software_environment" "software"             "software_environment"

``` r
table(str_extract_all(temp,"software_environment|software|environment"))
```

    ## 
    ##             software software_environment 
    ##                    1                    2



### 특정 word로 시작하는 글자를 찾고, 바꾸고 싶은 경우 

``` r

str_replace_all(r_wiki, "\\bg","&") # \\b~는 ~로 시작, ~\\b는 ~로 끝나는 것.
```

    ## [1] "R is a programming language and software environment for statistical computing and &raphics supported by the R Foundation for Statistical Computing. The R language is widely used among statisticians and data miners for developing statistical software and data analysis. Polls, surveys of data miners, and studies of scholarly literature databases show that R's popularity has increased substantially in recent years.\nR is a GNU package. The source code for the R software environment is written primarily in C, Fortran, and R. R is freely available under the GNU General Public License, and pre-compiled binary versions are provided for various operating systems. While R has a command line interface, there are several &raphical front-ends available."

``` r
str_extract_all(r_wiki, "[[:alpha:]]{1,}r\\b")
```

    ## [[1]]
    ## [1] "for"   "for"   "for"   "for"   "under" "for"

``` r
temp<-str_replace_all(r_wiki,"R\\b","R_computer.language_")
temp<-str_replace_all(r_wiki,"C\\b","C_computer.language_")

temp
```

    ## [1] "R is a programming language and software environment for statistical computing and graphics supported by the R Foundation for Statistical Computing. The R language is widely used among statisticians and data miners for developing statistical software and data analysis. Polls, surveys of data miners, and studies of scholarly literature databases show that R's popularity has increased substantially in recent years.\nR is a GNU package. The source code for the R software environment is written primarily in C_computer.language_, Fortran, and R. R is freely available under the GNU General Public License, and pre-compiled binary versions are provided for various operating systems. While R has a command line interface, there are several graphical front-ends available."

``` r
# 
r_wiki_para<-str_split(r_wiki, "\n")
print(r_wiki_para)
```

    ## [[1]]
    ## [1] "R is a programming language and software environment for statistical computing and graphics supported by the R Foundation for Statistical Computing. The R language is widely used among statisticians and data miners for developing statistical software and data analysis. Polls, surveys of data miners, and studies of scholarly literature databases show that R's popularity has increased substantially in recent years."
    ## [2] "R is a GNU package. The source code for the R software environment is written primarily in C, Fortran, and R. R is freely available under the GNU General Public License, and pre-compiled binary versions are provided for various operating systems. While R has a command line interface, there are several graphical front-ends available."

``` r
class(r_wiki_para) # r_wiki_para는 주머니이고, r_wiki_para[[1]]가 character vector이다. 
```

    ## [1] "list"

``` r
# split 함수 input에는 character vector가 들어가야한다. 
r_wiki_sent <- str_split(r_wiki_para[[1]], "\\. ")
```

### 특정 문장을 추출하고 싶은 경우

``` r

r_wiki_sent[[2]][1]
```

    ## [1] "R is a GNU package"

#### 2개 이상을 추출하고 싶은 경우

``` r

r_wiki_sent[[2]][c(1,4)]
```

    ## [1] "R is a GNU package"                                                                     
    ## [2] "While R has a command line interface, there are several graphical front-ends available."

``` r
# unlist 하고 싶은 경우
unlist(r_wiki_sent)[c(4,7)]
```

    ## [1] "R is a GNU package"                                                                     
    ## [2] "While R has a command line interface, there are several graphical front-ends available."

``` r
my2sentences<-unlist(r_wiki_sent)[c(4,7)]
my2sentences
```

    ## [1] "R is a GNU package"                                                                     
    ## [2] "While R has a command line interface, there are several graphical front-ends available."

### 문장의 단어 수를 출력하고 싶다. 

``` r

my2sentences1 <- str_split(my2sentences[[1]]," ") # I: character vector, O: list / character vector
my2sentences2 <- str_split(my2sentences[[2]]," ") 
length(my2sentences1[[1]]); length(my2sentences2[[1]])
```

    ## [1] 5
    
    ## [1] 13

### [갯수를 정해서 나누고 싶은 경우]

``` r

myfixed.short <- str_split_fixed(my2sentences, " ", 5)

# split 고급 함수, 갯수를 넉넉하게 해놓으면 matrix형태로 깔끔하게 분리
myfixed.long <- str_split_fixed(my2sentences, " ", 13)
myfixed.long
```

    ##      [,1]    [,2] [,3]  [,4]  [,5]      [,6]   [,7]         [,8]    [,9] 
    ## [1,] "R"     "is" "a"   "GNU" "package" ""     ""           ""      ""   
    ## [2,] "While" "R"  "has" "a"   "command" "line" "interface," "there" "are"
    ##      [,10]     [,11]       [,12]        [,13]       
    ## [1,] ""        ""          ""           ""          
    ## [2,] "several" "graphical" "front-ends" "available."

``` r
# list에 들어있는 여러개의 문장을 다루고 싶다.

r_wiki_sent
```

    ## [[1]]
    ## [1] "R is a programming language and software environment for statistical computing and graphics supported by the R Foundation for Statistical Computing"
    ## [2] "The R language is widely used among statisticians and data miners for developing statistical software and data analysis"                            
    ## [3] "Polls, surveys of data miners, and studies of scholarly literature databases show that R's popularity has increased substantially in recent years." 
    ## 
    ## [[2]]
    ## [1] "R is a GNU package"                                                                                                                     
    ## [2] "The source code for the R software environment is written primarily in C, Fortran, and R"                                               
    ## [3] "R is freely available under the GNU General Public License, and pre-compiled binary versions are provided for various operating systems"
    ## [4] "While R has a command line interface, there are several graphical front-ends available."

``` r
unlist(r_wiki_sent)
```

    ## [1] "R is a programming language and software environment for statistical computing and graphics supported by the R Foundation for Statistical Computing"
    ## [2] "The R language is widely used among statisticians and data miners for developing statistical software and data analysis"                            
    ## [3] "Polls, surveys of data miners, and studies of scholarly literature databases show that R's popularity has increased substantially in recent years." 
    ## [4] "R is a GNU package"                                                                                                                                 
    ## [5] "The source code for the R software environment is written primarily in C, Fortran, and R"                                                           
    ## [6] "R is freely available under the GNU General Public License, and pre-compiled binary versions are provided for various operating systems"            
    ## [7] "While R has a command line interface, there are several graphical front-ends available."

``` r
length(unlist(r_wiki_sent))
```

    ## [1] 7

``` r
rep(NA,length(unlist(r_wiki_sent)))
```

    ## [1] NA NA NA NA NA NA NA

``` r
length.sentences<-rep(NA,length(unlist(r_wiki_sent)))

length(length.sentences)
```

    ## [1] 7

``` r
for(i in 1:length(length.sentences)){
  length.sentences[i]<-length(unlist(str_split(unlist(r_wiki_sent)[i]," ")))
}

length.sentences #7
```

    ## [1] 21 18 21  5 16 20 13

``` r
max(length.sentences) #21
```

    ## [1] 21

### 특정 문자가 나오는 횟수 세기

``` r

str_count(r_wiki, "R") 
```

    ## [1] 9

``` r
r_wiki_para
```

    ## [[1]]
    ## [1] "R is a programming language and software environment for statistical computing and graphics supported by the R Foundation for Statistical Computing. The R language is widely used among statisticians and data miners for developing statistical software and data analysis. Polls, surveys of data miners, and studies of scholarly literature databases show that R's popularity has increased substantially in recent years."
    ## [2] "R is a GNU package. The source code for the R software environment is written primarily in C, Fortran, and R. R is freely available under the GNU General Public License, and pre-compiled binary versions are provided for various operating systems. While R has a command line interface, there are several graphical front-ends available."

``` r
str_count(r_wiki_para[[1]], "R")# I: vector
```

    ## [1] 4 5

``` r
# 대소문자 상관없게 하고싶다.
str_count(unlist(r_wiki_sent), "(s|S)tat")
```

    ## [1] 2 2 0 0 0 0 0

``` r
str_count(unlist(r_wiki_sent), "(s|S)tat[[:lower:]]{1,}")
```

    ## [1] 2 2 0 0 0 0 0

``` r
# 
name<-c("Joe", "Jack", "Jackie", "Jefferson")
donation<-c("$1","$111","$11111","$111111")

mydata<-data.frame(name,donation)
mydata
```

    ##        name donation
    ## 1       Joe       $1
    ## 2      Jack     $111
    ## 3    Jackie   $11111
    ## 4 Jefferson  $111111

### 정렬 하고싶은 경우 

``` r

name2<-str_pad(mydata$name, width = 15, side = "both", pad = " ") # padding = right, left, both
donation2<-str_pad(mydata$name, width = 15, side = "left", pad = "~")

mydata2<-data.frame(name2,donation2)
mydata2
```

    ##             name2       donation2
    ## 1       Joe       ~~~~~~~~~~~~Joe
    ## 2      Jack       ~~~~~~~~~~~Jack
    ## 3     Jackie      ~~~~~~~~~Jackie
    ## 4    Jefferson    ~~~~~~Jefferson

``` r
# 문자열 반복해서 생성하기
rep("soft", 3)
```

    ## [1] "soft" "soft" "soft"

``` r
str_dup("soft", 3)
```

    ## [1] "softsoftsoft"

``` r
# index를 이용해서 문자열 추출

class(unlist(r_wiki_sent))
```

    ## [1] "character"

``` r
str_sub(unlist(r_wiki_sent),1,200) # I:string vector O:
```

```R
## [1] "R is a programming language and software environment for statistical computing and graphics supported by the R Foundation for Statistical Computing"
## [2] "The R language is widely used among statisticians and data miners for developing statistical software and data analysis"                            
## [3] "Polls, surveys of data miners, and studies of scholarly literature databases show that R's popularity has increased substantially in recent years." 
## [4] "R is a GNU package"                                                                                                                                 
## [5] "The source code for the R software environment is written primarily in C, Fortran, and R"                                                           
## [6] "R is freely available under the GNU General Public License, and pre-compiled binary versions are provided for various operating systems"            
## [7] "While R has a command line interface, there are several graphical front-ends available."
```

``` r
# 문장 길이 
str_length(unlist(r_wiki_sent)) 
```

    ## [1] 147 119 146  18  88 135  87

``` R
nchar(unlist(r_wiki_sent))
```

```R
## [1] 147 119 146  18  88 135  87
```

### 패딩 제거

``` R

str_trim(mydata2$name2,side='right')->name3 # trim은 공백문자만 제거가능

# 공백 이외 다른 문자로 패딩한거 제거하기
str_replace_all(mydata2$donation2,'~',' ')->name3
str_trim(str_replace_all(mydata2$donation2,'~',' '), side='both')->donation3
mydata3<-data.frame(name3,donation3)
mydata3
```

```R
##             name3 donation3
## 1             Joe       Joe
## 2            Jack      Jack
## 3          Jackie    Jackie
## 4       Jefferson Jefferson
```
